# Servidor-video-streaming-RT-tiempo-real-python3
import cv2
from fastapi import FastAPI, WebSocket, WebSocketDisconnect
from fastapi.responses import HTMLResponse
import asyncio
import uvicorn
import psutil
import subprocess
import threading
import tkinter as tk
from tkinter import messagebox

# =================== Código del Servidor FastAPI ===================
app = FastAPI()

# Variables globales para la configuración
TARGET_WIDTH = 1300
TARGET_HEIGHT = 900
CAMERA_SOURCE = 0  # Valor por defecto para la cámara

# Se inicializa video_capture más adelante, una vez que se obtenga el valor de cámara
video_capture = None

clients = {}
client_id_counter = 0

@app.get("/")
async def get():
    return HTMLResponse(content=f"""
    <html>
        <head>
            <title>Video Streaming</title>
            <style>
                body {{ font-family: Arial, sans-serif; }}
            </style>
        </head>
        <body>
            <h1>Video Streaming</h1>
            <video id="video" width="{TARGET_WIDTH}" height="{TARGET_HEIGHT}" autoplay></video>
        </body>
    </html>
    """)

@app.websocket("/ws")
async def websocket_endpoint(websocket: WebSocket):
    global client_id_counter
    client_id_counter += 1
    client_id = client_id_counter
    clients[client_id] = {'websocket': websocket}
    await websocket.accept()
    
    try:
        while True:
            success, frame = video_capture.read()
            if not success:
                break
            frame_resized = cv2.resize(frame, (TARGET_WIDTH, TARGET_HEIGHT))
            ret, buffer = cv2.imencode('.jpg', frame_resized)
            if ret:
                for client in clients.values():
                    await client['websocket'].send_bytes(buffer.tobytes())
            await asyncio.sleep(0.01)
    except WebSocketDisconnect:
        del clients[client_id]

@app.post("/shutdown")
async def shutdown_server():
    video_capture.release()
    for client in clients.values():
        await client['websocket'].close()
    asyncio.get_event_loop().stop()  
    return {"message": "Servidor cerrado."}

def run_server(host: str, port: int, width: int, height: int, camera_source):
    global TARGET_WIDTH, TARGET_HEIGHT, CAMERA_SOURCE, video_capture
    TARGET_WIDTH = width
    TARGET_HEIGHT = height
    CAMERA_SOURCE = camera_source

    # Inicializamos el video_capture con la fuente de cámara configurada
    video_capture = cv2.VideoCapture(CAMERA_SOURCE)
    
    uvicorn.run(app, host=host, port=port)

# =================== Interfaz Gráfica con Tkinter ===================
def iniciar_servidor():
    ip = entry_ip.get().strip()
    puerto_str = entry_puerto.get().strip()
    ancho_str = entry_ancho.get().strip()
    alto_str = entry_alto.get().strip()
    cam_source_str = entry_cam.get().strip()

    if (not ip or not puerto_str.isdigit() or not ancho_str.isdigit() or 
        not alto_str.isdigit() or not cam_source_str):
        messagebox.showerror("Error", "Ingresa valores válidos para IP, Puerto, Ancho, Alto y Fuente de cámara.")
        return

    puerto = int(puerto_str)
    ancho = int(ancho_str)
    alto = int(alto_str)
    
    # Intentamos convertir la fuente de cámara a entero, si es posible.
    try:
        cam_source = int(cam_source_str)
    except ValueError:
        # Si no es entero, asumimos que es una ruta o URL
        cam_source = cam_source_str

    label_estado.config(text=f"Iniciando servidor en {ip}:{puerto} con {ancho}x{alto} y cámara '{cam_source}' ...")

    def ejecutar():
        try:
            run_server(ip, puerto, ancho, alto, cam_source)
        except Exception as e:
            messagebox.showerror("Error", str(e))

    threading.Thread(target=ejecutar, daemon=True).start()
    label_estado.config(text="Servidor iniciado.")

# Configuración de la ventana principal
ventana = tk.Tk()
ventana.title("Servidor FastAPI Configurable")

tk.Label(ventana, text="IP:").grid(row=0, column=0, padx=5, pady=5)
entry_ip = tk.Entry(ventana)
entry_ip.grid(row=0, column=1, padx=5, pady=5)
entry_ip.insert(0, "0.0.0.0")

tk.Label(ventana, text="Puerto:").grid(row=1, column=0, padx=5, pady=5)
entry_puerto = tk.Entry(ventana)
entry_puerto.grid(row=1, column=1, padx=5, pady=5)
entry_puerto.insert(0, "7100")

tk.Label(ventana, text="Ancho (px):").grid(row=2, column=0, padx=5, pady=5)
entry_ancho = tk.Entry(ventana)
entry_ancho.grid(row=2, column=1, padx=5, pady=5)
entry_ancho.insert(0, str(TARGET_WIDTH))

tk.Label(ventana, text="Alto (px):").grid(row=3, column=0, padx=5, pady=5)
entry_alto = tk.Entry(ventana)
entry_alto.grid(row=3, column=1, padx=5, pady=5)
entry_alto.insert(0, str(TARGET_HEIGHT))

tk.Label(ventana, text="Fuente de Cámara:").grid(row=4, column=0, padx=5, pady=5)
entry_cam = tk.Entry(ventana)
entry_cam.grid(row=4, column=1, padx=5, pady=5)
entry_cam.insert(0, "0")  # Valor por defecto

btn_conectar = tk.Button(ventana, text="Iniciar Servidor", command=iniciar_servidor)
btn_conectar.grid(row=5, column=0, columnspan=2, padx=5, pady=10)

label_estado = tk.Label(ventana, text="")
label_estado.grid(row=6, column=0, columnspan=2, padx=5, pady=5)

ventana.mainloop()

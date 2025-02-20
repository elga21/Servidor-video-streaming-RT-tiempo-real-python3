# Servidor-video-streaming-RT-tiempo-real-python3
Servidor RT streaming de video en tiempo real con python3

Este es una aplicaión la cual es un servidor de Video Streaming en tiempo real (RT) diseñado en python con una pequeña interfaz grafica realizada con tkinter para configuracion de los controles

Esta aplicación toma el video de una entrada serial que en el ordenador en este caso linux puede ser 0,1,2... y lo envia mediante una red de forma RT para que los clientes conectados a los spuertos del socket del servidor puedan visualizar el video que envia el servidor

Si necesitas la aplicación del cliente puedes descargarla de los diretorios de esta cuenta.

Sigue los siguinetes pasos si quieres crear tambien un ejecutable, es de resaltar que las instrucciones para el ejecutable esta en linux, pero la aplaicación puedes correr en window o linux

Este es una aplicaión la cual es un servidor de Video Streaming en tiempo real (RT) diseñado en python con una pequeña interfaz grafica realizada con tkinter para configuracion de los controles

Esta aplicación toma el video de una entrada serial que en el ordenador en este caso linux puede ser 0,1,2... y lo envia mediante una red de forma RT para que los clientes conectados a los spuertos del socket del servidor puedan visualizar el video que envia el servidor

si necesitas la aplicación del cliente puedes descargarla de los diretorios de esta cuenta.

Sigue los siguinetes pasos si quieres crear tambien un ejecutable, es de resaltar que las instrucciones para el ejecutable esta en linux, pero la aplaicación puedes correr en window o linux

1. Intala el módulo tkinter de phyton:
sudo apt-get install python3-tk

2. Copia el codigo fuente y guardalo como app.py:
3. Generar el ejecutable de phyton PyInstaller: 
pip3 install pyinstaller

4. Desde la terminal, en el directorio donde se encuentra server_gui_streaming.py, ejecuta:
pyinstaller --onefile app.py

(Esto creará una carpeta dist en la que encontrarás el ejecutable app.py)

5. Ejecutar el archivo generado:
Ya puedes hacer doble click (o desde terminal) en el ejecutable ubicado en dist/app.py para lanzar tu aplicación.
1. Intala el módulo tkinter de phyton:
sudo apt-get install python3-tk

2. Copia el codigo fuente y guardalo como app.py:
3. Generar el ejecutable de phyton PyInstaller: 
pip3 install pyinstaller

4. Desde la terminal, en el directorio donde se encuentra server_gui_streaming.py, ejecuta:
pyinstaller --onefile app.py

(Esto creará una carpeta dist en la que encontrarás el ejecutable app.py)

5. Ejecutar el archivo generado:
Ya puedes hacer doble click (o desde terminal) en el ejecutable ubicado en dist/app.py para lanzar tu aplicación.

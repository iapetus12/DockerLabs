Trust - DockerLabs
Información general
Plataforma: DockerLabs

Dificultad: Muy Fácil / Beginner

Categoría: Boot2Root

Objetivo: Obtener acceso como usuario mario y escalar privilegios a root.

1. Reconocimiento
1.1 Información inicial
IP: 172.18.0.2

Notas previas: La máquina presenta una página por defecto de Apache, lo que sugiere que el contenido relevante está oculto en directorios no indexados.

1.2 Enumeración de servicios
Puerto 22 (SSH): OpenSSH 9.2p1. Versión actualizada, se descarta explotación directa.

Puerto 80 (HTTP): Apache httpd 2.4.57. Muestra la página "It works".

2. Análisis y enfoque
2.1 Puntos interesantes
Directorio /secret.php: Encontrado mediante fuzzing. Revela un mensaje que menciona directamente a un usuario.

Sudo Permissions: El usuario inicial tiene permisos de sudo sobre binarios de edición de texto.

2.2 Hipótesis
Hipótesis 1: El nombre "Mario" en la web es un usuario válido para SSH. Un ataque de fuerza bruta con rockyou.txt podría darnos acceso.

Hipótesis 2: Una vez dentro, el vector de escalada será un binario mal configurado en los sudoers (GTFOBins).

3. Explotación
El camino comenzó con un fuzzing agresivo de directorios, ya que la raíz de la web no ofrecía información.

Paso 1: Uso de ffuf para descubrir el archivo secret.php.

Paso 2: Identificación del usuario mario tras leer el mensaje: "Hola Mario, esta web no se puede hackear".

Paso 3: Ataque de fuerza bruta por SSH utilizando Hydra. La contraseña fue hallada rápidamente debido a su baja complejidad.

Resultado: Acceso exitoso al sistema como el usuario mario.

4. Escalada de privilegios
Contexto inicial: Usuario mario con acceso via SSH.

Información clave: Al ejecutar sudo -l, se observa que el usuario puede ejecutar /usr/bin/vim como cualquier usuario (ALL) sin restricciones de parámetros.

Vector de escalada: Explotación de Vim mediante el uso del parámetro -c para ejecutar comandos de shell antes de cargar la interfaz del editor.

Resultado: Acceso total como root.

5. Notas finales y aprendizaje
Qué he aprendido: La importancia de no confiar en las páginas por defecto y que la enumeración de usuarios (User Enumeration) puede estar escondida en simples mensajes de texto.

Qué haría distinto: Podría haber probado un diccionario de nombres de usuario comunes antes del fuzzing para ganar tiempo.

Conceptos clave: Fuzzing de directorios, SSH Brute Force, GTFOBins (Sudo Exploitation).

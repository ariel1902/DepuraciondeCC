#!/data/data/com.termux/files/usr/bin/python
import os
from datetime import datetime

# Verificar permisos de escritura
def verificar_permisos_escritura(carpeta):
    try:
        prueba_archivo = os.path.join(carpeta, "prueba.txt")
        with open(prueba_archivo, 'w') as f:
            f.write("prueba")
        os.remove(prueba_archivo)
        return True
    except PermissionError:
        print("Error: No tienes permisos para escribir en esta carpeta. Ejecuta 'termux-setup-storage' y prueba de nuevo.")
        return False
    except Exception as e:
        print(f"Error al verificar permisos: {e}")
        return False

# Arte ASCII para "Depuración de CC"
def mostrar_arte_ascii():
    print("""
    ╔════════════════════════════════════╗
    ║    Depuración de CC - Termux       ║
    ║════════════════════════════════════║
    """)

# Mostrar menú principal
def mostrar_menu():
    print("\nMenú Principal:")
    print("1. Separar lives")
    print("2. Organizar por fechas")
    print("3. Guardar archivo")
    print("4. Salir del programa")
    return input("Selecciona una opción (1-4): ")

# Validar formato de tarjeta
def validar_formato_tarjeta(linea, es_nueva_lista=False):
    partes = linea.strip().split("|")
    if es_nueva_lista:
        return len(partes) == 4 and all(partes)  # CC | MES | AÑO | CVV
    else:
        if len(partes) != 5 or not all(partes):
            return False
        checker = partes[4].strip()
        return checker in ["> Live Card", "> Died Card", "> Unknow Card"]

# Opción 1: Separar lives
def separar_lives(lista_tarjetas):
    print("\nIngresa tu lista de tarjetas (máximo 100 filas). Presiona Enter una vez para finalizar:")
    print("Formato: 4830310088657755 | 119 | 08 | 2031 | > Live Card")
    nuevas_tarjetas = []
    while len(nuevas_tarjetas) < 100:
        linea = input()
        if linea == "":
            break
        if validar_formato_tarjeta(linea):
            nuevas_tarjetas.append(linea)
        else:
            print("Formato inválido. Ejemplo:  KernCard")
    if len(nuevas_tarjetas) > 100:
        print("Error: Se excedió el límite de 100 filas.")
        return lista_tarjetas
    # Filtrar solo las tarjetas con "> Live Card"
    lista_tarjetas = [linea for linea in nuevas_tarjetas if "> Live Card" in linea]
    print(f"Operación exitosa: Se encontraron {len(lista_tarjetas)} tarjetas Live.")
    input("Presiona Enter para volver al menú principal...")
    return lista_tarjetas

# Opción 2: Organizar por fechas
def organizar_por_fechas(lista_tarjetas):
    opcion = input("\n¿Quieres usar la lista actual (1) o cargar una nueva lista (2)? (1/2): ")
    if opcion == "1" and not lista_tarjetas:
        print("Error: No hay lista actual para procesar.")
        input("Presiona Enter para volver al menú principal...")
        return lista_tarjetas
    elif opcion == "2":
        print("\nIngresa tu lista de tarjetas (máximo 100 filas). Presiona Enter una vez para finalizar:")
        print("Formato: 4830310088657755 | 08 | 2031 | 119")
        nuevas_tarjetas = []
        while len(nuevas_tarjetas) < 100:
            linea = input()
            if linea == "":
                break
            if validar_formato_tarjeta(linea, es_nueva_lista=True):
                nuevas_tarjetas.append(linea)
            else:
                print("Formato inválido. Ejemplo: 4830310088657755 | 08 | 2031 | 119")
        if len(nuevas_tarjetas) > 100:
            print("Error: Se excedió el límite de 100 filas.")
            input("Presiona Enter para volver al menú principal...")
            return lista_tarjetas
        lista_tarjetas = nuevas_tarjetas
    try:
        fecha_limite = datetime(2025, 7, 1)
        tarjetas_filtradas = []
        for linea in lista_tarjetas:
            partes = linea.strip().split("|")
            mes = partes[2].strip() if not partes[2].startswith(">") else partes[1].strip()
            año = partes[3].strip() if not partes[3].startswith(">") else partes[2].strip()
            if not (mes.isdigit() and año.isdigit()):
                continue
            mes = int(mes)
            año = int(año)
            if mes < 1 or mes > 12 or año < 2025 or año > 2100:
                continue
            fecha_tarjeta = datetime(año, mes, 1)
            if fecha_tarjeta > fecha_limite:
                tarjetas_filtradas.append(linea)
        # Ordenar por año y luego por mes en orden ascendente
        tarjetas_filtradas = sorted(tarjetas_filtradas, key=lambda x: (int(x.split("|")[3 if "> Live Card" in x else 2]), int(x.split("|")[2 if "> Live Card" in x else 1])))
        print(f"Operación exitosa: Se encontraron {len(tarjetas_filtradas)} tarjetas con fecha válida, ordenadas por año y mes.")
        input("Presiona Enter para volver al menú principal...")
        return tarjetas_filtradas
    except Exception as e:
        print(f"Error al procesar fechas: {e}")
        input("Presiona Enter para volver al menú principal...")
        return lista_tarjetas

# Opción 3: Guardar archivo
def guardar_archivo(lista_tarjetas):
    if not lista_tarjetas:
        print("Error: No hay datos para guardar.")
        input("Presiona Enter para volver al menú principal...")
        return
    print("\nOpciones de guardado:")
    print("1. Guardar en ubicación por defecto (/storage/emulated/0/Downloads/CC_lives)")
    print("2. Guardar en carpeta específica")
    opcion = input("Selecciona una opción (1/2): ")
    if opcion == "1":
        carpeta = "/storage/emulated/0/Downloads/CC_lives"
        if not verificar_permisos_escritura(carpeta):
            print("Sugerencia: Usa una carpeta dentro de Termux, como /data/data/com.termux/files/home/CC_lives")
            input("Presiona Enter para volver al menú principal...")
            return
        try:
            os.makedirs(carpeta, exist_ok=True)
        except Exception as e:
            print(f"Error al crear carpeta por defecto: {e}")
            input("Presiona Enter para volver al menú principal...")
            return
    elif opcion == "2":
        carpeta = input("Ingresa la ruta completa de la carpeta (ejemplo: /data/data/com.termux/files/home/CC_lives): ")
        if not os.path.exists(carpeta):
            print("Error: La carpeta especificada no existe.")
            input("Presiona Enter para volver al menú principal...")
            return
        if not verificar_permisos_escritura(carpeta):
            input("Presiona Enter para volver al menú principal...")
            return
    else:
        print("Opción inválida.")
        input("Presiona Enter para volver al menú principal...")
        return
    nombre_archivo = input("Ingresa el nombre del archivo (sin .txt): ")
    ruta_completa = os.path.join(carpeta, f"{nombre_archivo}.txt")
    try:
        with open(ruta_completa, 'w') as file:
            for linea in lista_tarjetas:
                file.write(linea + '\n')
        print(f"Archivo guardado exitosamente en {ruta_completa}")
    except Exception as e:
        print(f"Error al guardar el archivo: {e}")
    input("Presiona Enter para volver al menú principal...")

# Programa principal
def main():
    lista_tarjetas = []
    while True:
        mostrar_arte_ascii()
        opcion = mostrar_menu()
        if opcion == "1":
            lista_tarjetas = separar_lives(lista_tarjetas)
        elif opcion == "2":
            lista_tarjetas = organizar_por_fechas(lista_tarjetas)
        elif opcion == "3":
            guardar_archivo(lista_tarjetas)
        elif opcion == "4":
            print("Saliendo del programa...")
            break
        else:
            print("Opción inválida. Por favor, selecciona una opción válida (1-4).")
            input("Presiona Enter para continuar...")

if __name__ == "__main__":
    main()

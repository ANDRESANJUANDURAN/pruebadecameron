rt sqlite3 # Conexión a la base de datos (se creará si no existe) 
conn = sqlite3.connect('hotelesDecameron.db') 
c = conn.cursor() 
# Creación de la tabla de Hoteles 
# c.execute(''' 
# CREATE TABLE IF NOT EXISTS Hoteles
#  ( id INTEGER PRIMARY KEY AUTOINCREMENT, 
# nombre TEXT NOT NULL, 
# direccion TEXT,
#  ciudad TEXT, 
# pais TEXT, 
# datos_tributarios TEXT 
# ) 
# ''')

# Creación de la tabla de Tipos de Habitación
c.execute('''
CREATE TABLE IF NOT EXISTS TiposHabitacion (
id INTEGER PRIMARY KEY AUTOINCREMENT,
tipo TEXT NOT NULL,
hotelDecameron_id INTEGER,
FOREIGN KEY (hotel_id) REFERENCES Hoteles(id)
) 
''')

# Creación de la tabla de Acomodaciones
c.execute('''
CREATE TABLE IF NOT EXISTS Acomodaciones (
id INTEGER PRIMARY KEY AUTOINCREMENT,
tipo TEXT NOT NULL,
tipo_habitacion_id INTEGER,
FOREIGN KEY (tipo_habitacion_id) REFERENCES TiposHabitacion(id)
) 
''')

conn.commit()

# Función para agregar un hotel
def agregar_hotel(nombre, direccion, ciudad, pais, datos_tributarios):
    c.execute('INSERT INTO Hoteles (nombre, direccion, ciudad, pais, datos_tributarios) VALUES (?, ?, ?, ?, ?)',
              (nombre, direccion, ciudad, pais, datos_tributarios))
    conn.commit()

    # Función para agregar un tipo de habitación
    def agregar_tipo_habitacion(tipo, hotel_id):
        if tipo in ["Estándar", "Junior", "Suite"]:
            c.execute('INSERT INTO TiposHabitacion (tipo, hotel_id) VALUES (?, ?)', (tipo, hotel_id))
            conn.commit()
        else:
            print("Tipo de habitación no válido")

    # Función para agregar una acomodación
    def agregar_acomodacion(tipo, tipo_habitacion_id):
        # Obtención del tipo de habitación
        c.execute('SELECT tipo FROM TiposHabitacion WHERE id = ?', (tipo_habitacion_id,))
        tipo_habitacion = c.fetchone()[0]

        # Validación de la acomodación
        if tipo_habitacion == "Estándar" and tipo in ["Sencilla", "Doble"]:
            c.execute('INSERT INTO Acomodaciones (tipo, tipo_habitacion_id) VALUES (?, ?)', (tipo, tipo_habitacion_id))
        elif tipo_habitacion == "Junior" and tipo in ["Triple", "Cuádruple"]:
            c.execute('INSERT INTO Acomodaciones (tipo, tipo_habitacion_id) VALUES (?, ?)', (tipo, tipo_habitacion_id))
        elif tipo_habitacion == "Suite" and tipo in ["Sencilla", "Doble", "Triple"]:
            c.execute('INSERT INTO Acomodaciones (tipo, tipo_habitacion_id) VALUES (?, ?)', (tipo, tipo_habitacion_id))
        else:
            print("Acomodación no válida para el tipo de habitación")

        conn.commit()

        # Ejemplos de uso
        agregar_hotel("Hotel Ejemplo", "123 Calle Falsa", "Cartagena", "Colombia", "NIT: 12345678-9")
        agregar_tipo_habitacion("Estándar", 1)
        agregar_acomodacion("Sencilla", 1)

        # Cierre de la conexión a la base de datos
        conn.close()
        

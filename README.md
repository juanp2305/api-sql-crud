from flask import Flask, jsonify, request
import pymysql

app = Flask(__name__)

# Configuración de la conexión a la base de datos
connection = pymysql.connect(
    host='localhost',
    user='root',
    password='',
    database='api_flask'
)

@app.route('/usuarios', methods=['GET'])
def get_usuarios():
    return jsonify(query('SELECT * FROM usuarios'))

@app.route('/usuarios/<int:id>', methods=['GET'])
def get_usuario(id):
    return jsonify(query('SELECT * FROM usuarios WHERE id = %s', (id,)))

@app.route('/usuarios', methods=['POST'])
def create_usuario():
    usuario = request.json
    query('INSERT INTO usuarios (nombre, email) VALUES (%s, %s)', (usuario['nombre'], usuario['email']))
    return jsonify({'success': True}), 201

@app.route('/usuarios/<int:id>', methods=['PUT'])
def update_usuario(id):
    usuario = request.json
    query('UPDATE usuarios SET nombre = %s, email = %s WHERE id = %s', (usuario['nombre'], usuario['email'], id))
    return jsonify({'success': True})

@app.route('/usuarios/<int:id>', methods=['DELETE'])
def delete_usuario(id):
    query('DELETE FROM usuarios WHERE id = %s', (id,))
    return jsonify({'success': True})

# ... (implementar las rutas para productos)

def query(sql, args=()):
    cursor = connection.cursor()
    cursor.execute(sql, args)
    if sql.startswith('SELECT'):
        data = cursor.fetchall()
    else:
        connection.commit()
        data = None
    cursor.close()
    return data

if __name__ == '__main__':
    app.run(debug=True)

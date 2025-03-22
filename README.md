pip install flask
from flask import Flask, request, jsonify

app = Flask(__name__)

# Banco de dados fictício (poderia ser substituído por um banco real)
users = []

# Rota para criar um novo perfil de usuário
@app.route('/user', methods=['POST'])
def create_user():
    data = request.get_json()
    
    # Validar entrada
    if 'name' not in data or 'age' not in data or 'email' not in data:
        return jsonify({'error': 'Faltando dados obrigatórios'}), 400
    
    # Criar o perfil do usuário
    user = {
        'id': len(users) + 1,  # Simulando um ID único
        'name': data['name'],
        'age': data['age'],
        'email': data['email'],
        'bio': data.get('bio', '')  # Campo opcional
    }
    
    users.append(user)
    
    return jsonify(user), 201

# Rota para listar todos os usuários
@app.route('/users', methods=['GET'])
def get_users():
    return jsonify(users)

# Rota para obter o perfil de um usuário específico
@app.route('/user/<int:user_id>', methods=['GET'])
def get_user(user_id):
    user = next((u for u in users if u['id'] == user_id), None)
    if user is None:
        return jsonify({'error': 'Usuário não encontrado'}), 404
    return jsonify(user)

# Rota para atualizar o perfil de um usuário
@app.route('/user/<int:user_id>', methods=['PUT'])
def update_user(user_id):
    user = next((u for u in users if u['id'] == user_id), None)
    if user is None:
        return jsonify({'error': 'Usuário não encontrado'}), 404
    
    data = request.get_json()
    
    user['name'] = data.get('name', user['name'])
    user['age'] = data.get('age', user['age'])
    user['email'] = data.get('email', user['email'])
    user['bio'] = data.get('bio', user['bio'])
    
    return jsonify(user)

# Rota para excluir um usuário
@app.route('/user/<int:user_id>', methods=['DELETE'])
def delete_user(user_id):
    global users
    users = [u for u in users if u['id'] != user_id]
    return jsonify({'message': 'Usuário excluído com sucesso'}), 200

if __name__ == '__main__':
    app.run(debug=True)

curl -X POST http://127.0.0.1:5000/user -H "Content-Type: application/json" -d '{"name": "João", "age": 28, "email": "joao@example.com"}'
curl http://127.0.0.1:5000/users

curl -X PUT http://127.0.0.1:5000/user/1 -H "Content-Type: application/json" -d '{"name": "João Silva", "age": 29, "email": "joaosilva@example.com"}'
curl -X DELETE http://127.0.0.1:5000/user/1

# Rota para criar um novo perfil de usuário
@app.route('/user', methods=['POST'])
def create_user():
    data = request.get_json()
    
    # Validar entrada
    if 'name' not in data or 'age' not in data or 'email' not in data or 'interests' not in data or 'location' not in data:
        return js({'error': 'Faltando dados obrigatórios'}), 400
    
    # Criar o perfil do usuárioonify
    user = {
        'id': len(users) + 1,  # Simulando um ID único
        'name': data['name'],
        'age': data['age'],
        'email': data['email'],
        'bio': data.get('bio', ''),  # Campo opcional
        'interests': data['interests'],  # Novo campo
        'location': data['location']  # Novo campo
    }
    
    users.append(user)
    
    return jsonify(user), 201
@app.route('/match/<int:user_id>', methods=['GET'])
def match_user(user_id):
    user = next((u for u in users if u['id'] == user_id), None)
    if user is None:
        return jsonify({'error': 'Usuário não encontrado'}), 404
    
    # Encontrar usuários com interesses similares ou dentro de uma faixa etária
    matched_users = []
    for potential_match in users:
        if potential_match['id'] != user_id:  # Não se combinar com ele mesmo
            # Comparar interesses
            common_interests = set(user['interests']).intersection(set(potential_match['interests']))
            # Considerando que o match é baseado em 5 anos de diferença de idade
            age_difference = abs(user['age'] - potential_match['age'])
            
            if common_interests and age_difference <= 5:
                matched_users.append(potential_match)
    
    return jsonify(matched_users)
# Banco de dados fictício para likes e deslikes
likes = []

# Rota para curtir ou rejeitar um perfil
@app.route('/like', methods=['POST'])
def like_profile():
    data = request.get_json()
    
    if 'user_id' not in data or 'liked_id' not in data or 'liked' not in data:
        return jsonify({'error': 'Faltando dados obrigatórios'}), 400
    
    # Armazenar o like ou deslike
    like = {
        'user_id': data['user_id'],
        'liked_id': data['liked_id'],
        'liked': data['liked'],  # True para curtir, False para rejeitar
        'timestamp': datetime.utcnow().isoformat()
    }
    
    likes.append(like)
    
    return jsonify(like), 201

# Rota para ver os likes de um perfil
@app.route('/likes/<int:user_id>', methods=['GET'])
def get_likes(user_id):
    user_likes = [like for like in likes if like['liked_id'] == user_id]
    return jsonify(user_likes)

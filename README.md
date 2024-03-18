- 👋 Hi, I’m @Jose01teste
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...

<!---
Jose01teste/Jose01teste is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->

import socket
import threading

# Configurações do servidor
host = 'localhost'
port = 12345

# Criando o socket do servidor
server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.bind((host, port))
server.listen()

# Lista para manter as conexões dos clientes
clients = []
nicknames = []

# Envia a mensagem para todos os clientes conectados
def broadcast(message):
    for client in clients:
        client.send(message)

# Trata as mensagens dos clientes
def handle(client):
    while True:
        try:
            # Recebendo a mensagem do cliente
            message = client.recv(1024)
            broadcast(message)
        except:
            # Removendo e fechando a conexão se algo der errado
            index = clients.index(client)
            clients.remove(client)
            client.close()
            nickname = nicknames[index]
            broadcast(f'{nickname} saiu do chat!'.encode('utf-8'))
            nicknames.remove(nickname)
            break

# Recebe as conexões dos clientes
def receive():
    while True:
        client, address = server.accept()
        print(f"Conectado com {str(address)}")

        # Solicita e armazena o apelido do cliente
        client.send('NICK'.encode('utf-8'))
        nickname = client.recv(1024).decode('utf-8')
        nicknames.append(nickname)
        clients.append(client)

        # Informa que o cliente se conectou
        print(f'Apelido do cliente é {nickname}')
        broadcast(f'{nickname} entrou no chat!'.encode('utf-8'))
        client.send('Conectado ao servidor!'.encode('utf-8'))

        # Inicia o tratamento das mensagens em uma nova thread
        thread = threading.Thread(target=handle, args=(client,))
        thread.start()

print("Servidor está ouvindo...")
receive()

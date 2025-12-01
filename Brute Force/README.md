# Brute Force Attack (Ataque de Força Bruta)

Um ataque de força bruta consiste em tentar várias combinações de usuário/senha ou chaves até encontrar a correta, normalmente de forma automatizada.
É uma técnica simples, porém muito utilizada por atacantes para quebrar senhas fracas ou serviços mal configurados.

## 🎯 Objetivo do desafio

- Entender, na prática, como funciona um ataque de força bruta.
- Testar ferramentas de automação em um **ambiente controlado**.
- Reforçar a importância de:
  - políticas de senha fortes;
  - bloqueio por tentativas inválidas;
  - monitoramento e logs de autenticação.

Preparação do laboratorio!

Precisamos usar os seguintes comandos para que possamos realizar a criação das wordlists.

Este vai criar os usuários:
echo -e "user\nuser123\nadmin\nroot\nusuario\nmsfadmin" > users.txt

E este para possíveis senhas:
echo -e "admin\nuser\nroot\npassword\n123456\nmsfadmin" > password.txt


Scaneando as portas e serviços com Nmap.
Após criar as 2 máquinas virtuais precisamos ver qual IP de cada uma delas.

Comando para ver o IP:<br>
ip a<br>
Máquina Kali Linux:

  ![ip_kali](https://github.com/user-attachments/assets/0760dc6d-31ee-454b-8edf-5aff65647bf3)

Máquina Metasploitable:

![ipMetasploitable](https://github.com/user-attachments/assets/10155b4a-6d74-4543-b968-b9aea72fe36b)

Agora validaremos se tem comunicação entre as máquinas com o ping de cada uma:
Kali Linux para a MetaSploitable:
IMAGEM AQUI

MetaSploitable para a Kali Linux:
IMAGEM AQUI

Vemos que as máquinas se enxergam, e isso se deve por estarem na mesma rede, a configuração para isso é deixar ambas as máquinas com o adaptador em modo "only-host"
IMAGEM AQUI



## ⚠️ Aviso importante

Todo o conteúdo deste diretório é destinado **exclusivamente para estudos em ambientes autorizados**.  
Nunca utilize técnicas de força bruta contra sistemas ou serviços sem permissão expressa do responsável, pois isso é **ilegal** e antiético.

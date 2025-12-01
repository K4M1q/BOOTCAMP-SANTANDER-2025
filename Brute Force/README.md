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
Máquina Kali Linux:<br>
![ip_kali](https://github.com/user-attachments/assets/0760dc6d-31ee-454b-8edf-5aff65647bf3)

Máquina Metasploitable:<br>

![ipMetasploitable](https://github.com/user-attachments/assets/10155b4a-6d74-4543-b968-b9aea72fe36b)

Agora validaremos se tem comunicação entre as máquinas com o ping de cada uma:
Kali Linux para a MetaSploitable:
![kalitometa jpeg](https://github.com/user-attachments/assets/df565c75-d958-4ecb-9424-5e0510a510e7)

MetaSploitable para a Kali Linux:<br>
![metatokali jpeg](https://github.com/user-attachments/assets/b2d4fcd2-2ae5-47ca-aefa-5e8dd4c716ed)


Vemos que as máquinas se enxergam, e isso se deve por estarem na mesma rede, a configuração para isso é deixar ambas as máquinas com o adaptador em modo "only-host"<br>
![onlyhost jpeg](https://github.com/user-attachments/assets/9c30d333-1e9b-484f-aaa3-09fbe327722c)


Temos a confirmação que as máquinas estão com ip da mesma rede e se comunicando normalmente.

NMAP E VARREDURA DAS PORTAS 🔍

No Kali, já temos essa ferramenta instalado por padrão. Sabemos que o IP da máquina alvo é "192.168.56.101" agora vamos aplicar uma varredura nesse IP e ver quais as portas estão abertas com o seguinte comando:<br>
nmap -sV -p 21,22,80,445,139 192.168.56.101<br>
![scanportmeta jpeg](https://github.com/user-attachments/assets/830f82f8-a2c0-47c1-b646-4709a4410a9d)

Teremos o seguinte resultado:<br>
![resultscanmeta jpeg](https://github.com/user-attachments/assets/78ba4e2e-935a-4fd3-ac5c-02b26fe4ff43)

Podemos ver que temos as portas abertas e com isso vamos usar aquela wordlist na qual já criamos para efetuar o ataque com o medusa no próximo passo.

UTILIZANDO O MEDUSA 🐍

O medusa, assim como nmap já vem pré-instalado no kali, usaremos o seguinte comando para realizar o ataque no ftp com a porta 21 aberta:<br>
![ataqueftp jpeg](https://github.com/user-attachments/assets/4d249438-9542-4723-a2aa-70d2581344c0)

Temos o "SUCCESS" no ataque com a credencial:<br>
user: msfadmin<br>
senha: msfadmin

E com isso podemos acessar o ftp usando comando no terminal:
ftp 192.168.56.101

E conectar com as credenciais coletadas:<br>
![conexaoftp jpeg](https://github.com/user-attachments/assets/b50173a2-ed00-4482-a248-57d49ab985e8)

ATAQUE SMB 🖧⚠️<br>
Vamos realizar agora o ataque SMB na página web que a máquina do metasploitable tem, a URL é a seguinte:<br>
http://192.168.56.101/dvwa/login.php<br>
![sitemetasploitable jpeg](https://github.com/user-attachments/assets/a0bd2b20-e58d-4bb3-92c6-53fc51e905c2)<br>

Tentaremos login com:<br>
admin<br>
senha: kali

Porém não foi feito login, mas com o inspecionar e na aba de network com a opção de "Request" vemos que aparece os dados do usuário:<br>
![requestlogin jpeg](https://github.com/user-attachments/assets/34940030-2653-4c51-90d2-402ae53851c7)<br>
Isso é uma falha na qual poderemos se aproveitar, vemos que no site também mostra a condição de se teve sucesso ou não, informando "Login failed" então vamos usar isso ao nosso favor.

Vamos usar o comando:<br>
medusa -h 192.168.56.101 -U users.txt -P password.txt -M http \ <br>
-m PAGE:'/dvwa/login.php' \ <br>
-m FORM:'username=^USER^&password=^PASS^&login=Login' \ <br>
-m 'FAIL= Login Falied -t 6 <br>
![credencialsite jpeg](https://github.com/user-attachments/assets/1a93bbc8-8cb3-456f-98e8-e69a1384d2c1)<br>

![ataquesitemedusa jpeg](https://github.com/user-attachments/assets/b6e926b5-1e1a-4ce0-be5f-0a881324c673)<br>
Com isso temos o acesso ao login do usuário admin, para uma infra isso é extremamente perigoso.

ATAQUE SMB + PASSWORD SPRAYING 🔐🖧

Esse ataque consiste em tentar acessar contas de maneira massiva e controlada, sem que tenhamos bloqueio, isso ocorre devido ao ataque tentar uma senha para várias contas diferentes ao inves de tentar várias senhas para a mesma conta, onde o código identifica e bloqueia a requisição antes de qualquer sucesso.
Vamos dar andamento no projeto.

Usaremos o seguinte comando:
enum4linux -a 192.168.56.101 | tee enum4_output.txt<br>
![enum4linuxterminal jpeg](https://github.com/user-attachments/assets/6fe309c5-b32e-4489-9d2a-4b25ea675262)<br>

Após apertar enter e o scan parar pedindo senha:<br>
![senhaenum jpeg](https://github.com/user-attachments/assets/ec88ab3e-be08-4295-9d22-85da6da0c8ea)<br>
Basta teclar enter, não afeta nada no scan.

Abra com o comando "less enum4_output.txt"

E após isso teremos a base para o nosso ataque, vamos para o comando com as wordlists já criadas anteriormente (devido ao usuário já estar lá)
Caso o user não esteja, basta usar os users que ver no arquivo que o enum4linux trouxe.<br>
![usersenum4linux jpeg](https://github.com/user-attachments/assets/d318af3c-f4ce-4d0b-8d3a-dfd092a40b34)<br>

Todos os usuários dessa lista podem ser um possível vertor de ataque, então, seria interessante adicionar todos para testar as senhas mais padrões possiveis.

Com isso, temos acesso ao SMB Client, conforme testado.<br>
![loginsmbsuccess jpeg](https://github.com/user-attachments/assets/b35ba464-5cdd-4457-a778-3e7a4a858fac)<br>
Comando para teste:<br>
smbclient -L //192.168.56.101 -U msfadmin<br>

Conclusão: Ataques SMB, Password Spraying e FTP 🔐

Quando olhamos para ataques envolvendo SMB, SMB com Password Spraying e FTP, fica claro que todos eles têm algo em comum: são portas e serviços muito usados no dia a dia das empresas, mas que acabam se tornando pontos fracos quando não recebem a atenção necessária.

O SMB, por ser responsável por compartilhamento de arquivos e autenticação dentro da rede, vira alvo fácil quando há senhas fracas ou versões antigas do protocolo ainda ativas. Quando isso se junta ao Password Spraying — aquela técnica de testar uma senha comum em vários usuários — o risco cresce ainda mais. Basta uma conta com senha previsível para abrir caminho dentro da rede.

O FTP entra no mesmo pacote: é um protocolo antigo, sem criptografia, e que ainda aparece bastante por aí. Se estiver mal configurado, vira uma porta aberta para vazamento de dados ou acessos indevidos.

No fim das contas, o problema não está apenas nos protocolos, mas em como eles são usados. Credenciais fracas, serviços antigos, falta de monitoramento e configurações deixadas “como vieram” criam exatamente o cenário que um atacante espera encontrar.

Reforçar senhas, atualizar serviços, revisar permissões e manter uma rotina de auditoria simples já faz uma diferença enorme na segurança. Pequenos ajustes evitam grandes dores de cabeça — e colocam a empresa vários passos à frente de qualquer tentativa de ataque.


## ⚠️ Aviso importante

Todo o conteúdo deste diretório é destinado **exclusivamente para estudos em ambientes autorizados**.  
Nunca utilize técnicas de força bruta contra sistemas ou serviços sem permissão expressa do responsável, pois isso é **ilegal** e antiético.

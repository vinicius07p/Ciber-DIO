# 🛡️ Simulação de Ataque de Força Bruta com Medusa e Kali Linux

Este projeto documenta a execução prática de auditoria de segurança e testes de intrusão (PenTest) simulando ataques de força bruta, password spraying e enumeração de usuários em um ambiente controlado de laboratório vulnerável.

O objetivo deste laboratório foi entender na prática o funcionamento das ferramentas de ataque, analisar o impacto de credenciais fracas e propor medidas defensivas eficazes para ambientes corporativos.

---

## 🛠️ Ambiente de Laboratório

O ambiente foi virtualizado no Oracle VirtualBox utilizando uma rede privada virtual (Host-Only / Rede Interna), garantindo isolamento total da rede externa:

- **Atacante:** Kali Linux
- **Vítima:** Metasploitable 2
- **Rede:** 192.168.56.0/24

---

## 🚀 Etapas do Projeto

### 1. Enumeração de Serviços e Usuários (SMB)
Antes de iniciar os ataques de força bruta, realizou-se a enumeração de contas e compartilhamentos SMB no alvo via `enum4linux`, registrando os dados capturados para análise.

**Comando:**
bash
enum4linux -a 192.168.56.102 | tee enum4_output.txt

2. Criação de Wordlists Customizadas

Com base nos usuários enumerados e nas senhas mais comuns utilizadas em ambientes vulneráveis, foram criadas wordlists específicas para o ataque de Password Spraying.

Criação da lista de usuários (smb_users.txt):
Bash

echo -e "user\nmsfadmin\nservice" > smb_users.txt

Criação da lista de senhas (senhas_spray.txt):
Bash

echo -e "password\n123456\nwelcome123\nmsfadmin" > senhas_spray.txt

3. Ataque de Password Spraying em SMB com Medusa

Com as listas estruturadas, utilizou-se o Medusa focado no módulo smbnt para testar as combinações de credenciais com baixo número de tentativas por usuário, evitando bloqueios de conta.

Comando:
Bash

medusa -h 192.168.56.102 -U smb_users.txt -P senhas_spray.txt -M smbnt -t 2 -v 5

4. Validação de Acesso via smbclient

Após o Medusa identificar credenciais válidas (msfadmin:msfadmin), a autenticação foi validada diretamente no serviço SMB.

Comando:
Bash

smbclient -L //192.168.56.102 -U msfadmin

🔒 Medidas de Mitigação e Prevenção

Para proteger ambientes corporativos contra os ataques demonstrados, recomendam-se as seguintes boas práticas de segurança:

  Política de Senhas Fortes: Exigir senhas complexas (mínimo de 12 caracteres, incluindo letras maiúsculas, minúsculas, números e caracteres especiais).

  Bloqueio de Contas (Account Lockout Policy): Configurar limite de tentativas incorretas de login para mitigar brute force e password spraying.
    Autenticação Multi-Fator (MFA): Implementar MFA em todas as portas de entrada e serviços críticos da rede.
    Desabilitação do Protocolo SMBv1: Manter apenas versões seguras do SMB (SMBv2/v3) e restringir o acesso anônimo a compartilhamentos de rede.
    Monitoramento e SIEM: Configurar alertas automáticos para detectar múltiplos erros de autenticação originados do mesmo IP ou tentando múltiplos usuários em curto espaço de tempo.

📌 Autor

Desenvolvido para o Desafio Prático da DIO (Digital Innovation One).

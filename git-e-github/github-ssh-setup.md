# 🔑 Como criar e vincular uma chave SSH ao GitHub

Usar uma chave SSH permite que você se conecte ao GitHub sem precisar digitar seu usuário e senha (ou token) toda vez que for fazer um `push` ou `pull`. 

Este guia mostra o passo a passo de como gerar uma nova chave e adicioná-la à sua conta do GitHub.

---

## 1. Verificando chaves SSH existentes
Antes de gerar uma nova chave, verifique se você já não possui uma. Abra o seu terminal (ou Git Bash no Windows) e digite:

```bash
ls -al ~/.ssh

```

Verifique se existem arquivos como `id_rsa.pub`, `id_ecdsa.pub` ou `id_ed25519.pub`. Se você já tiver um e quiser usá-lo, pode pular para o **Passo 4**. Caso contrário, continue lendo.

## 2. Gerando uma nova chave SSH

No terminal, cole o comando abaixo, substituindo pelo e-mail usado na sua conta do GitHub. O algoritmo `ed25519` é o mais moderno e seguro recomendado atualmente.

```bash
ssh-keygen -t ed25519 -C "seu_email@exemplo.com"

```

O terminal vai te perguntar onde salvar o arquivo:

> *Enter a file in which to save the key (/home/user/.ssh/id_ed25519):*

Apenas pressione **Enter** para aceitar o local padrão.

Em seguida, ele pedirá uma senha (passphrase):

> *Enter passphrase (empty for no passphrase):*

Você pode digitar uma senha para maior segurança ou apenas pressionar **Enter** duas vezes para deixar sem senha (assim não precisará digitá-la ao usar o Git).

## 3. Adicionando a chave ao `ssh-agent`

O `ssh-agent` é um programa que gerencia suas chaves. Primeiro, inicie-o em segundo plano:

```bash
eval "$(ssh-agent -s)"

```

*(Você deverá ver uma mensagem como `Agent pid 59566`)*

Agora, adicione a chave privada que você acabou de criar ao agente:

```bash
ssh-add ~/.ssh/id_ed25519

```

## 4. Copiando a chave pública

Você precisa copiar o conteúdo da sua chave **pública** (o arquivo terminado em `.pub`) para colar no GitHub.

Você pode exibir a chave no terminal e copiá-la manualmente:

```bash
cat ~/.ssh/id_ed25519.pub

```

Ou usar comandos para copiar direto para a área de transferência:

* **Windows (Git Bash):** `clip < ~/.ssh/id_ed25519.pub`
* **Mac:** `pbcopy < ~/.ssh/id_ed25519.pub`
* **Linux:** `xclip -sel clip < ~/.ssh/id_ed25519.pub` *(requer o xclip instalado)*

## 5. Adicionando a chave no GitHub

1. Acesse o [GitHub](https://github.com) e faça login.
2. No canto superior direito, clique na sua foto de perfil e vá em **Settings** (Configurações).
3. No menu lateral esquerdo, clique em **SSH and GPG keys**.
4. Clique no botão verde **New SSH key** (Nova chave SSH).
5. Preencha os campos:
* **Title:** Dê um nome que identifique o computador (ex: *Meu Notebook Pessoal* ou *PC do Escritório*).
* **Key type:** Deixe como *Authentication Key*.
* **Key:** Cole a chave pública que você copiou no passo anterior.


6. Clique em **Add SSH key**.

## 6. Testando a conexão

Para garantir que tudo foi configurado corretamente, volte ao terminal e digite:

```bash
ssh -T git@github.com

```

Você poderá ver um aviso perguntando se confia na autenticidade do host. Digite `yes` e pressione Enter.

Se tudo deu certo, você verá uma mensagem parecida com esta:

> *Hi SeuUsuario! You've successfully authenticated, but GitHub does not provide shell access.*

🎉 **Pronto!** Seu computador está configurado e vinculado ao GitHub via SSH.

---

**Dica:** Da próxima vez que for clonar um repositório, lembre-se de usar o link **SSH** (que começa com `git@github.com:...`) em vez do link HTTPS.

```

```
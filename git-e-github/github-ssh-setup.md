# 🔑 Como criar e vincular uma chave SSH ao GitHub

Usar uma chave SSH permite que você se conecte ao GitHub sem precisar digitar seu usuário e token de acesso pessoal a cada `push` ou `pull`.

Este guia mostra o passo a passo completo para verificar, gerar, configurar e testar sua chave SSH, além de como migrar seus repositórios existentes de HTTPS para SSH.

---

## 1. Verificando chaves SSH existentes

Antes de gerar uma nova chave, verifique se você já possui alguma no computador:

* **No Linux, macOS ou Windows (Git Bash):**
  ```bash
  ls -al ~/.ssh
  ```

* **No Windows (PowerShell):**
  ```powershell
  ls ~\.ssh -Force
  ```
  *(ou `dir ~\.ssh`)*

Verifique se existem arquivos como `id_ed25519.pub` ou `id_rsa.pub`. Se você já tiver um arquivo `.pub` e quiser usá-lo, pode pular direto para o [Passo 4](#4-copiando-a-chave-pública). Se a pasta não existir ou estiver vazia, continue para o próximo passo.

---

## 2. Gerando uma nova chave SSH

No terminal, execute o comando abaixo substituindo pelo e-mail associado à sua conta do GitHub. O algoritmo `ed25519` é o padrão moderno e recomendado pela segurança e performance.

```bash
ssh-keygen -t ed25519 -C "seu_email@exemplo.com"
```

1. **Local do arquivo:** O terminal perguntará onde salvar a chave:
   ```text
   Enter a file in which to save the key (/home/user/.ssh/id_ed25519):
   ```
   Apenas pressione **Enter** para aceitar o local padrão.

2. **Senha (Passphrase):** Em seguida, ele perguntará se deseja criar uma senha:
   ```text
   Enter passphrase (empty for no passphrase):
   ```
   * **Recomendado para praticidade:** Apenas pressione **Enter** duas vezes para deixar sem senha (assim o Git não pedirá senha a cada comando).
   * **Para segurança extra:** Digite uma senha e confirme.

---

## 3. Adicionando a chave ao `ssh-agent` (Opcional se não tiver senha)

> 💡 **Você colocou senha na chave no Passo 2?**
> * **Se NÃO colocou senha (apertou Enter):** Você pode **pular este passo** e ir direto para o [Passo 4](#4-copiando-a-chave-pública)! O Git encontrará sua chave padrão automaticamente.
> * **Se colocou senha:** Siga as instruções abaixo para que o sistema memorize a sua senha e não precise digitá-la a todo momento.

### No Linux, macOS ou Windows (Git Bash):
```bash
# Inicia o agente
eval "$(ssh-agent -s)"

# Adiciona a chave privada ao agente
ssh-add ~/.ssh/id_ed25519
```

### No Windows (PowerShell):
No Windows, o serviço do agente costuma vir desativado por padrão (o que causa o erro `1058`). Para ativá-lo:

1. Abra o **PowerShell como Administrador** e execute:
   ```powershell
   Get-Service ssh-agent | Set-Service -StartupType Automatic
   Start-Service ssh-agent
   ```
2. Depois, no seu terminal normal, adicione a chave:
   ```powershell
   ssh-add ~\.ssh\id_ed25519
   ```

---

## 4. Copiando a chave pública

Você precisa copiar apenas o conteúdo da sua chave **pública** (o arquivo que termina com `.pub`).

Você pode copiar diretamente para a área de transferência com o comando referente ao seu sistema:

* **Windows (Git Bash):**
  ```bash
  clip < ~/.ssh/id_ed25519.pub
  ```
* **Windows (PowerShell):**
  ```powershell
  Get-Content ~\.ssh\id_ed25519.pub | Set-Clipboard
  ```
* **macOS:**
  ```bash
  pbcopy < ~/.ssh/id_ed25519.pub
  ```
* **Linux (X11):**
  ```bash
  xclip -selection clipboard < ~/.ssh/id_ed25519.pub
  ```
* **Linux (Wayland):**
  ```bash
  wl-copy < ~/.ssh/id_ed25519.pub
  ```

> 💡 **Dica:** Se preferir, você também pode apenas exibir o conteúdo no terminal com `cat ~/.ssh/id_ed25519.pub` e selecioná-lo manualmente com o mouse.

---

## 5. Adicionando a chave no GitHub

1. Acesse o [GitHub](https://github.com) e faça login.
2. No canto superior direito, clique na sua foto de perfil e selecione **Settings** (Configurações).
3. No menu lateral esquerdo, vá em **SSH and GPG keys**.
4. Clique no botão verde **New SSH key** (Nova chave SSH).
5. Preencha os campos:
   * **Title:** Um nome descritivo para seu computador (ex: *Notebook Pessoal*, *PC de Casa*).
   * **Key type:** Mantenha como **Authentication Key**.
   * **Key:** Cole o conteúdo da chave pública copiado no passo anterior.
6. Clique em **Add SSH key** (se solicitado, confirme com sua senha ou 2FA do GitHub).

---

## 6. Testando a conexão

Para validar se tudo deu certo, volte ao terminal e digite:

```bash
ssh -T git@github.com
```

* Se for seu primeiro acesso, o terminal perguntará se você confia no host (`Are you sure you want to continue connecting (yes/no/[fingerprint])?`). Digite **`yes`** e pressione **Enter**.
* Em seguida, você deve receber uma mensagem de sucesso como:
  > *Hi SeuUsuario! You've successfully authenticated, but GitHub does not provide shell access.*

🎉 **Pronto!** Sua chave SSH está devidamente configurada e comunicando com o GitHub.

---

## 7. Usando SSH no seu dia a dia

### Em novos repositórios
Ao clonar qualquer projeto novo, copie o link na aba **SSH** (que começa com `git@github.com:...`) em vez da aba HTTPS:

```bash
git clone git@github.com:usuario/nome-do-repositorio.git
```

### Em repositórios que já estão no seu computador
Se você já clonou repositórios anteriormente usando HTTPS, o Git continuará pedindo usuário/token neles até você atualizar a URL remota:

1. Entre na pasta do repositório no terminal.
2. Verifique a URL remota atual:
   ```bash
   git remote -v
   ```
3. Se começar com `https://github.com/...`, altere para SSH:
   ```bash
   git remote set-url origin git@github.com:seu-usuario/nome-do-repositorio.git
   ```
4. Pronto! O próximo `git push` ou `git pull` já utilizará a sua chave SSH automaticamente.
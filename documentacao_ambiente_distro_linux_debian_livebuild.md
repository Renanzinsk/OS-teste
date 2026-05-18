# Documentação do Ambiente de Desenvolvimento da Distro Linux

## Objetivo
Criar um ambiente de desenvolvimento para construir uma distro Linux baseada em Debian usando `live-build`.

---

# Estrutura do Ambiente

## Host
Sistema principal:
- Fedora Linux

Funções:
- Armazenar ISOs
- Testar builds
- Gerenciar arquivos
- Executar VMs

---

## VM
Sistema da máquina virtual:
- Debian

Funções:
- Ambiente de build
- Geração das ISOs
- Desenvolvimento da distro

---

# Instalação da VM Debian

Foi utilizada uma VM com:
- 4 GB RAM
- 2 CPUs
- 30 GB disco

ISO utilizada:
- Debian amd64

---

# Configuração inicial do Debian

Atualizar sistema:

```bash
apt update && apt upgrade -y
```

Instalar ferramentas principais:

```bash
apt install live-build git curl wget vim nano debootstrap arch-test squashfs-tools xorriso openssh-server -y
```

---

# Correção do PATH do root

O Debian minimal veio sem `/usr/sbin` e `/sbin` no PATH.

Correção temporária:

```bash
export PATH=$PATH:/usr/sbin:/sbin
```

Correção permanente:

Editar:

```bash
nano ~/.bashrc
```

Adicionar no final:

```bash
export PATH=$PATH:/usr/sbin:/sbin
```

Recarregar:

```bash
source ~/.bashrc
```

---

# Estrutura do Projeto

Criar diretório do projeto:

```bash
mkdir -p ~/projeto-minha-distro
cd ~/projeto-minha-distro
```

Inicializar live-build:

```bash
lb config
```

---

# Primeiro Build

Executar:

```bash
lb build
```

ISO gerada:

```text
live-image-amd64.hybrid.iso
```

---

# Pacotes adicionados à distro

Criar:

```bash
mkdir -p config/package-lists
nano config/package-lists/live.list.chroot
```

Conteúdo:

```text
xfce4
lightdm
network-manager
network-manager-gnome
firefox-esr
vlc
thunar
mousepad
```

---

# Rebuild da ISO

Quando modificar configurações:

```bash
lb clean
lb config
lb build
```

Observação:
- Sempre executar os comandos na raiz do projeto.
- Nunca executar dentro de `config/` ou `package-lists/`.

---

# Git e GitHub

Inicializar Git:

```bash
git init
```

Configurar usuário:

```bash
git config --global user.name "Renan"
git config --global user.email "SEU_EMAIL"
```

---

# Arquivo .gitignore

Criar:

```bash
nano .gitignore
```

Conteúdo:

```text
*.iso

binary/
cache/
chroot/
local/

*.packages
*.contents
*.files
*.buildinfo
*.changes

live-image-*
binary.modified_timestamps
chroot.files
chroot.packages.install
chroot.packages.live

*.log
```

---

# Workflow Git

Adicionar arquivos:

```bash
git add .
```

Commit:

```bash
git commit -m "Descrição"
```

Push:

```bash
git push
```

---

# Transferência da ISO para o Fedora

## Ativar SSH na VM Debian

```bash
sudo apt install openssh-server -y
sudo systemctl enable --now ssh
```

---

## Transferir ISO para o host Fedora

No Fedora:

```bash
scp renan@IP_DA_VM:/home/renan/projeto-minha-distro/live-image-amd64.hybrid.iso ~/vm/
```

Exemplo:

```bash
scp renan@192.168.122.153:/home/renan/projeto-minha-distro/live-image-amd64.hybrid.iso ~/vm/
```

---

# Estrutura importante do projeto

## Pastas que DEVEM existir no GitHub

```text
auto/
config/
.gitignore
```

---

## Pastas que NÃO devem ir para o GitHub

```text
binary/
cache/
chroot/
local/
*.iso
```

---

# Observações Importantes

- A primeira build demora bastante.
- Builds futuras ficam mais rápidas devido ao cache.
- `lb clean` remove partes do cache.
- `lb clean --purge` limpa completamente o ambiente.
- XFCE foi escolhido por ser leve e adequado para hardware antigo.
- O tamanho da ISO (~1.2 GB) é normal para uma distro live com XFCE e Firefox.

---

# Workflow Atual

```text
Fedora (host)
↓
Debian VM
↓
live-build
↓
ISO
↓
Transferência SCP
↓
Testes
```

---

# Próximos Passos Futuros

- Adicionar temas
- Adicionar wallpapers
- Criar scripts automáticos
- Personalizar boot splash
- Otimizar tamanho da ISO
- Criar instalador personalizado
- Criar repositórios próprios
- Automatizar builds


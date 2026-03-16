# Fedora Live CD

Repositório com o código fonte do **Lorax** (ferramenta oficial do Fedora para criar Live CDs) + **livemedia-creator**.

## Requisitos

```bash
# Fedora/RHEL/CentOS
sudo dnf install lorax virt-install libvirt libdnf

# Dependências adicionais para criar Live CD
sudo dnf install anaconda-boot-iso xorriso squashfs-tools
```

## Criar Live CD com Kickstart

### 1. Criar arquivo kickstart (exemplo básico)

```bash
# Arquivo: minha-live.ks
cat > minha-live.ks << 'EOF'
lang pt_BR.UTF-8
keyboard us
timezone America/Sao_Paulo --utc

rootpw --plaintext senha123
bootloader --location=mbr

# Partições
autopart --nohome --noboot
clearpart --all --initlabel

# Repositório
url --url=https://download.fedoraproject.org/pub/fedora/linux/releases/41/Server/x86_64/os/

# Pacotes mínimos
%packages
@^minimal-environment
kernel
dracut-config-generic
squashfs-tools
-xfsprogs
%end

# Pós-instalação
%post
# Configurações personalizadas aqui
%end

# Tipo de Live CD
livecd --rootfs-size=3
EOF
```

### 2. Criar a ISO

```bash
# Com virtualização (recomendado)
sudo livemedia-creator \
    --make-iso \
    --iso=/path/to/Fedora-Server-dvd-x86_64-41-1.4.iso \
    --ks=minha-live.ks \
    --output=/tmp/livecd/

# Sem virtualização (modo texto)
sudo livemedia-creator \
    --make-iso \
    --no-virt \
    --ks=minha-live.ks \
    --output=/tmp/livecd/
```

## Adicionar Calamares (Instalador)

Para adicionar o Calamares ao seu Live CD:

```bash
# No arquivo kickstart, adicione:
%packages
calamares
%end

# Após criar a ISO, configure o Calamares:
# Edite os arquivos em /etc/calamares/ dentro da imagem
```

## Estrutura do Repositório

```
├── src/
│   ├── bin/
│   │   └── livemedia-creator  # Comando principal
│   └── pylorax/               # Bibliotecas Python
├── share/
│   └── templates.d/99-generic/  # Templates de boot
├── docs/
│   └── *.ks                   # Exemplos de kickstart
└── etc/
    └── lorax.conf             # Configuração
```

## Links Úteis

- [Documentação oficial do Lorax](https://weldr.io/lorax/livemedia-creator.html)
- [Fedora Wiki - Livemedia-creator](https://docs.fedoraproject.org/en-US/quick-docs/creating-and-using-a-live-installation-image/)
- [Documentação do Calamares](https://calamares.io/)

## Licença

GPL-3.0 - Same as lorax upstream

# OS Rice & Configuration Backup

Complete backup of Arch Linux ricing configuration, dotfiles, and packages. This repository contains everything needed to replicate the setup on a fresh installation.

## Repository Structure

```
dotfiles/
├── scripts/              # Installation and setup scripts
│   ├── pre-install.sh   # Partition formatting & mounting (interactive)
│   └── post-install.sh  # Configuration & package installation
├── configs/             # Application configuration files
│   ├── hypr/            # Hyprland window manager config
│   ├── waybar/          # Waybar status bar config
│   ├── kitty/           # Kitty terminal config
│   ├── zsh/             # Zsh shell config, plugins, history
│   ├── rofi/            # Rofi app launcher config
│   ├── dunst/           # Dunst notification daemon
│   ├── starship/        # Starship prompt config
│   ├── vim/             # Vim/Neovim config
│   ├── swaylock/        # Swaylock screen locker
│   ├── gtk-3.0/         # GTK3 themes & settings
│   ├── qt5ct/           # QT5 config tool settings
│   ├── qt6ct/           # QT6 config tool settings
│   ├── swappy/          # Screenshot tool config
│   ├── hyde/            # HyDE theme selector
│   ├── fastfetch/       # System info display
│   ├── uwsm/            # Wayland session env vars
│   └── [config files]   # Individual config files (.list, rc files)
├── dotfiles/            # Home directory dotfiles
│   ├── .zshrc           # Zsh rc file
│   ├── .bashrc          # Bash rc file
│   └── .gitconfig       # Git configuration
├── packages/            # Package lists for restoration
│   ├── pacman-packages.txt  # Main pacman packages
│   └── aur-packages.txt     # AUR packages
└── README.md            # This file
```

## Installation Flow

### Step 1: Pre-Installation (Arch ISO)

```bash
cd /tmp && git clone <this-repo> rice
bash rice/scripts/pre-install.sh
```

The script will:
- List available disks
- Prompt for disk and partition selections (no hardcoding!)
- Format and mount partitions interactively
- Display final partition layout

### Step 2: Arch Installation

Follow standard Arch Linux installation:
```bash
pacstrap -K /mnt base linux linux-firmware
genfstab -U /mnt >> /mnt/etc/fstab
arch-chroot /mnt
```

### Step 3: Post-Base Install (Inside chroot)

Set up GRUB and basic system:
```bash
pacman -S grub efibootmgr
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
grub-mkconfig -o /boot/grub/grub.cfg
```

### Step 4: Hyde Setup (After first boot into new Arch)

Run the Hyde project's installation script:
```bash
git clone https://github.com/hyde-project/hyde ~/.config/hyde
cd ~/.config/hyde && bash install.sh
```

This installs the main ricing components (Hyprland, Waybar, themes, etc.).

### Step 5: Post-Installation Configuration

After Hyde finishes, run this repo's post-install script:

```bash
# Clone repo to home or other location
git clone <this-repo> ~/dotfiles
bash ~/dotfiles/scripts/post-install.sh ~/dotfiles
```

The script will:
- Copy all configuration files to `~/.config/`
- Copy dotfiles to home directory (with backups)
- Prompt for package installation (with confirmation)
- Install both pacman and AUR packages

## What Gets Installed

### Main Applications (37 packages)
- **Development**: `base-devel`, `git`, `vim`, `jdk17-openjdk`, `bun`, `go`, `nodejs`
- **Utilities**: `android-tools`, `fzf`, `ripgrep`, `ripgrep-all`, `eza`, `htop`, `jq`
- **Terminals & Shells**: `kitty`, `zsh`, `zsh-theme-powerlevel10k-git`
- **Shell Utilities**: `starship`, `cliphist`, `playerctl`, `udiskie`, `brightnessctl`
- **Multimedia**: `vlc`, `spotify`
- **Networking**: `ngrok`, `docker`, `docker-desktop`
- **System**: `blueman`, `pavucontrol`, `pass`, `pokemon-colorscripts-git`
- **Dev Tools**: `visual-studio-code-bin`, `ebook-tools`
- **Download/Media**: `qbittorrent`, `firefox`
- **Ricing**: `rofi`, `antigravity`

**Note**: Dependencies and libraries are NOT included in the package list. They will be installed automatically as dependencies when you install the main packages.

### What This Repo Backs Up (Customizations Only)

This repo captures **only your personal customizations** — files that HyDE does not overwrite on updates. HyDE itself manages the base configs (animations, shaders, waybar modules/layouts/styles, rofi themes, dunst defaults, etc.).

**Your customizations:**
- `hypr/hyprland.lua` — Dual-monitor layout (DP-3 + eDP-1), custom keybinds (SUPER+F/C/SHIFT+C/O), Dolphin tiled
- `hypr/themes/theme.conf` — Catppuccin-Mocha theme with custom border colors
- `zsh/.zshrc` — Powerlevel10k prompt, `code` alias, desktop notification hook
- `zsh/user.zsh` — Disabled HyDE's starship prompt, added sudo plugin
- `zsh/conf.d/binds.zsh` — Ctrl+Arrow word navigation
- `hyde/hyde.conf` — Active theme selector (Catppuccin Mocha)
- `fastfetch/config.jsonc` — Japanese-themed system info display
- `uwsm/env-hyprland.d/00-hyde.sh` — HiDPI scaling, Wayland/Electron flags
- `kitty/` — Oxocarbon theme, JetBrainsMono font override
- `waybar/config.jsonc` — Pill-style top bar layout

### Configurations
- Full Hyprland, Waybar, Kitty, and shell configurations
- Rofi launcher config with custom themes
- Dunst notifications with custom styling
- Vim/Neovim setup
- Zsh with plugins, Powerlevel10k prompt, and history
- GTK/QT theming
- Swaylock screen locker config
- HyDE theme selector (Catppuccin Mocha)
- Fastfetch system info display (Japanese-themed)
- UWSM environment variables (HiDPI scaling, Wayland/Electron flags)

## Manual Steps

Some things may need manual setup:

1. **Git Configuration** (update `.gitconfig`)
   ```bash
   git config --global user.name "Your Name"
   git config --global user.email "your@email.com"
   ```

2. **Password Manager** (if using pass)
   ```bash
   pass init your-gpg-key-id
   ```

3. **Theme Customization** 
   - Edit `configs/hyde/hyde.conf` to change the active theme
   - Modify `configs/hypr/hyprland.lua` for keybinds and monitor layout
   - Edit `configs/waybar/config.jsonc` for waybar customization

## Important Notes

- **Backups**: The post-install script creates `.bak.TIMESTAMP` backups of overwritten files
- **Hyde First**: Always run HyDE's `install.sh` before the post-install script
- **AUR Packages**: Requires `yay` or `paru` to be installed
- **HyDE Updates**: Running `install.sh -r` will overwrite base configs but preserves files in this repo (keybinds, themes, shell customizations)
- **Partition Selection**: The pre-install script is fully interactive - no hardcoded values

## Updating This Repository

To backup new changes:

```bash
# Backup current configs
cp -r ~/.config/hypr ~/dotfiles/configs/
cp -r ~/.config/waybar ~/dotfiles/configs/
# ... etc for other configs

# Backup updated dotfiles
cp ~/.zshrc ~/dotfiles/dotfiles/
cp ~/.bashrc ~/dotfiles/dotfiles/

# Backup new packages
pacman -Q | awk '{print $1}' > ~/dotfiles/packages/pacman-packages.txt
yay -Qm > ~/dotfiles/packages/aur-packages.txt
```

## License

Feel free to use, modify, and share this configuration for your own ricing needs.

## Credits

- [Hyprland](https://hyprland.org/) - Dynamic tiling Wayland compositor
- [Waybar](https://github.com/Alexays/Waybar) - Highly customizable Wayland bar
- [Arch Linux](https://archlinux.org/)
- [Hyde Project](https://github.com/HyDE-Project/HyDE) - Rice installation automation

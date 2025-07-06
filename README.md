# wsl2-custom-kernel

**wsl2-custom-kernel** est un guide et un ensemble de scripts pour compiler, personnaliser et installer un noyau Linux sur mesure pour WSL2, incluant la gestion complète des modules sous Windows 11 avec Ubuntu.

## Objectif

Permettre à tout utilisateur avancé d’installer un noyau Linux personnalisé et ses modules dans WSL2, en suivant une procédure claire, reproductible et compatible avec les dernières versions de Windows et Linux.

## Prérequis

- Windows 11 avec WSL2 installé
- Distribution Ubuntu sous WSL2
- Droits administrateur sous Windows et sudo sous Ubuntu
- Espace disque suffisant

## Dépendances

À installer dans Ubuntu (WSL2) :

```
sudo apt update
sudo apt install build-essential flex bison dwarves libssl-dev libelf-dev cpio qemu-utils
```

## Résumé des commandes

| Étape | Commande |
|-------|----------|
| Télécharger les sources | `wget https://www.kernel.org/pub/linux/kernel/v6.x/linux-6.15.5.tar.gz`<br>`tar -xf linux-6.15.5.tar.gz`<br>`cd linux-6.15.5` |
| Récupérer la config WSL2 | `wget https://github.com/microsoft/WSL2-Linux-Kernel/raw/linux-msft-wsl-6.6.y/Microsoft/config-wsl` |
| (Optionnel) Personnaliser la config | `make menuconfig KCONFIG_CONFIG=Microsoft/config-wsl` |
| Compiler le noyau et installer les modules | `make -j$(nproc) KCONFIG_CONFIG=Microsoft/config-wsl`<br>`make INSTALL_MOD_PATH="$PWD/modules" modules_install` |
| Générer le VHDX des modules | `sudo ./Microsoft/scripts/gen_modules_vhdx.sh "$PWD/modules" $(make -s kernelrelease) modules.vhdx` |
| Nettoyer les fichiers temporaires | `make clean && rm -r "$PWD/modules"` |
| Copier le noyau et les modules sous Windows | `cp arch/x86/boot/bzImage /mnt/c/Users/VOTRE_UTILISATEUR/`<br>`cp modules.vhdx /mnt/c/Users/VOTRE_UTILISATEUR/` |
| Configurer WSL2 | Modifier/créer `.wslconfig` dans `C:\Users\VOTRE_UTILISATEUR\` |
| Redémarrer WSL2 | `wsl --shutdown` |
| Vérifier l’installation | `uname -a`<br>`ls /lib/modules/` |

## Procédure détaillée

### 1. Télécharger les sources du noyau

```
wget https://www.kernel.org/pub/linux/kernel/v6.x/linux-6.15.5.tar.gz
tar -xf linux-6.15.5.tar.gz
cd linux-6.15.5
```

### 2. Récupérer la configuration WSL2

```
wget https://github.com/microsoft/WSL2-Linux-Kernel/raw/linux-msft-wsl-6.6.y/Microsoft/config-wsl
```

### 3. (Optionnel) Personnaliser la configuration

```
make menuconfig KCONFIG_CONFIG=Microsoft/config-wsl
```

### 4. Compiler le noyau et installer les modules

```
make -j$(nproc) KCONFIG_CONFIG=Microsoft/config-wsl
make INSTALL_MOD_PATH="$PWD/modules" modules_install
```

### 5. Générer le VHDX des modules (méthode automatique)

```
sudo ./Microsoft/scripts/gen_modules_vhdx.sh "$PWD/modules" $(make -s kernelrelease) modules.vhdx
```

### 6. Nettoyer les fichiers temporaires

```
make clean && rm -r "$PWD/modules"
```

### 7. Copier le noyau et les modules sous Windows

```
cp arch/x86/boot/bzImage /mnt/c/Users/VOTRE_UTILISATEUR/
cp modules.vhdx /mnt/c/Users/VOTRE_UTILISATEUR/
```

### 8. Configurer WSL2 pour utiliser le noyau et les modules personnalisés

Créer ou éditer le fichier `.wslconfig` dans `C:\Users\VOTRE_UTILISATEUR\` :

```
[wsl2]
kernel=C:\\Users\\VOTRE_UTILISATEUR\\bzImage
kernelModules=C:\\Users\\VOTRE_UTILISATEUR\\modules.vhdx
```

### 9. Redémarrer WSL2

Dans PowerShell ou l’invite de commandes Windows :

```
wsl --shutdown
```

Puis relancer votre distribution WSL2.

### 10. Vérifier l’installation

Dans Ubuntu (WSL2) :

```
uname -a
ls /lib/modules/
```

## Ressources

- Documentation officielle WSL2
- Dépôt officiel du noyau WSL2 (GitHub)
- kernel.org

## Licence

Ce projet est sous licence MIT. Voir le fichier LICENSE pour plus d’informations.

---

**Contributions bienvenues !**  
Pour toute question ou suggestion, merci d’ouvrir une issue ou une pull request.

---

## Citations et références Markdown

- [Formatage de texte avec Markdown – Aide Zendesk][1]
- [Syntaxe de base pour l'écriture et la mise en forme - GitHub Docs][2]
- [La syntaxe markdown - Framasoft Documentation][3]
- [Débuter avec Markdown - Programming Historian][4]
- [Le langage MarkDown | DevSecOps - Stephane Robert][6]
- [Markdown : le guide du langage de balisage léger - IONOS][7]

[1]: https://support.zendesk.com/hc/fr/articles/4408846544922-Formatage-de-texte-avec-Markdown
[2]: https://docs.github.com/fr/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax
[3]: https://docs.framasoft.org/fr/grav/markdown.html
[4]: https://programminghistorian.org/fr/lecons/debuter-avec-markdown
[6]: https://blog.stephane-robert.info/docs/developper/autres-langages/markdown/
[7]: https://www.ionos.fr/digitalguide/sites-internet/developpement-web/markdown/
```

Citations :
[1] Formatage de texte avec Markdown – Aide Zendesk https://support.zendesk.com/hc/fr/articles/4408846544922-Formatage-de-texte-avec-Markdown
[2] Syntaxe de base pour l'écriture et la mise en forme - GitHub Docs https://docs.github.com/fr/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax
[3] La syntaxe markdown - Framasoft Documentation https://docs.framasoft.org/fr/grav/markdown.html
[4] Débuter avec Markdown - Programming Historian https://programminghistorian.org/fr/lecons/debuter-avec-markdown
[5] Inclure des références bibliographiques dans R Markdown à l'aide ... https://quanti.hypotheses.org/2270
[6] Le langage MarkDown | DevSecOps - Stephane Robert https://blog.stephane-robert.info/docs/developper/autres-langages/markdown/
[7] Markdown : le guide du langage de balisage léger - IONOS https://www.ionos.fr/digitalguide/sites-internet/developpement-web/markdown/
[8] Les bases de Markdown - Zettlr Docs https://docs.zettlr.com/fr/reference/markdown-basics/
[9] Principales syntaxes Markdown - Geek's Git https://fcoulombeau.github.io/markdown/

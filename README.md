# pesxgta

Tommy Vercetti (GTA Vice City) dans un vrai match de PES 6 : il entre sur le terrain, et les joueurs peuvent être abattus pendant que le match continue.

Le projet est écrit en Rust. **Direction : tout porter dans un moteur Rust depuis le code décompilé des deux jeux.** Un mod chargé dans le vrai PES 6 sert d'instrument de mesure : il lit le ballon, l'arbitre et les 22 joueurs, enregistre des matchs réels image par image et capture l'écran ; chaque partie portée en Rust doit reproduire ces enregistrements.

Issu du projet Chaos FC (octobre 2026).

## Aucun fichier des jeux dans ce dépôt

pesxgta lit tout depuis **vos propres installations** de GTA Vice City (PC) et de PES 6 (PC). Aucun modèle, texture, son, fichier extrait ni code décompilé n'est versionné : le `.gitignore` bloque les extensions des jeux et la CI échoue si l'une d'elles est suivie.

## Mise en route

Prérequis Windows : rustup, et les Build Tools de Visual Studio (« Développement Desktop en C++ »). La toolchain est figée dans `rust-toolchain.toml`.

1. Copier `config.example.toml` en `config.toml` et y indiquer les dossiers des deux jeux.
2. Vérifier les chemins :

   ```sh
   cargo run -p asset-tools -- check-config
   ```

3. Lancer le moteur Rust (stade de PES 6, ballon, joueur contrôlé) :

   ```sh
   cargo run -p game
   ```

### Le mod du vrai PES 6

PES 6 est un jeu 32 bits : le mod se compile pour `i686-pc-windows-msvc` (`rustup target add i686-pc-windows-msvc`).

```sh
cargo build -p pes6-mod --release --target i686-pc-windows-msvc
cargo run -p asset-tools -- mod install      # pose dinput8.dll et chaos-fc-mod.toml à côté de PES6.exe
cargo run -p asset-tools -- mod uninstall    # les retire
```

En jeu, le mod écrit à côté de PES6.exe un journal (`chaos-fc-mod.log`), un enregistrement par session (`chaos-fc-record-<horodatage>.cfrc`) et des captures (`chaos-fc-capture-<n>.bmp`).

## Organisation

| Crate | Rôle |
| --- | --- |
| `formats-rw` | Formats de Vice City : IMG, DFF, TXD, IFP, SFX |
| `formats-pes` | Formats de PES 6 : AFS, compression, textures, modèles, stades |
| `asset-bridge` | Types neutres, configuration, cache d'extraction |
| `bevy-bridge` | Types neutres vers Bevy |
| `retarget` | Squelettes, échelles, axes, retargeting d'animations |
| `asset-tools` | Outils en ligne de commande et visualiseur |
| `game` | Le moteur Rust (Bevy, Rapier) |
| `pes6-mod` | Le mod chargé dans le vrai PES6.exe |

Documentation (en français) :

- `docs/BRIEF.md` : le brief, les décisions et la feuille de route ;
- `docs/pes6-code.md` : la carte du code décompilé de PES 6 ;
- `docs/pes6-mod.md` : le plan et le journal du mod ;
- `docs/formats/` : les formats de fichiers, champ par champ.

Le code décompilé lui-même reste hors du dépôt (projets Ghidra locaux).

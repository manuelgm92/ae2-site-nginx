# Zensical

## Instalación de Zensical

Primero crea un entorno virtual y actívalo, luego instala `zensical` con `pip`:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install zensical
```

Una vez instalado, para arrancar la documentación en el directorio desde el directorio donde se desea alojar, ejecuta el comando:

`zensical new .`

Esto crea la siguiente estructura:

```md
.
├─ .github/workflows
│  └─ docs.yml
├─ docs/
│  ├─ index.md
│  └─ markdown.md
└─ zensical.toml
```
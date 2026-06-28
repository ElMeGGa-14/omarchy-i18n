# omarchy-i18n

Localización para [Omarchy](https://github.com/icl/omarchy): cambia idioma del sistema, teclado,
formato de reloj y traduce los menús de Omarchy.

## Instalación

```bash
git clone https://github.com/ElMeGGa-14/omarchy-i18n.git
cd omarchy-i18n

# Script principal
cp bin/omarchy-i18n ~/.local/bin/
chmod +x ~/.local/bin/omarchy-i18n

# Traducciones
mkdir -p ~/.config/omarchy/i18n
cp i18n/*.json ~/.config/omarchy/i18n/

# Vigilancia automática (opcional)
cp systemd/* ~/.config/systemd/user/
systemctl --user daemon-reload
systemctl --user enable --now omarchy-i18n-check.timer
```

## Uso

```bash
omarchy-i18n              # Asistente interactivo
omarchy-i18n --check      # Buscar cadenas sin traducir
omarchy-i18n --watch      # Modo silencioso (notifica si hay novedades)
omarchy-i18n --install-watch  # Generar systemd timer
```

Después de `omarchy update`, ejecuta `omarchy-i18n --check` para ver si hay
cadenas nuevas que traducir. El timer systemd revisa automáticamente cada día.

## Añadir un idioma

Solo hay que crear `~/.config/omarchy/i18n/<lang>.json` siguiendo el formato de
los existentes (con `phrases` para frases completas y `words` para el diccionario
palabra-por-palabra). El script nunca necesita editarse.

## Idiomas disponibles

- Español (es), Francés (fr), Italiano (it), Portugués (pt), Ruso (ru), Chino (zh)

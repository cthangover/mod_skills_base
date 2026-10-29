# Skills Engine — la colección de cartas de habilidad del jugador

Skills Engine guarda las habilidades que el jugador ha reunido y las enseña en el panel **Habilidades** en forma de cartas. Una habilidad puede abrir una posibilidad en otra mecánica (una «compuerta» pasiva), entregar una carta de acción nueva para los combates y sumar números al **efecto de colección**: el total de todas las habilidades reunidas.

Forma parte de Cthangover, donde el jugador empieza con una sola habilidad, **Tratante de esclavos**.

## El panel Habilidades

Se abre con el icono de habilidades en la barra de herramientas.

![El panel Habilidades en Cthangover: la carta Tratante de esclavos (épica, pasiva, +10 % de probabilidad de reclutamiento) en la cuadrícula, su descripción en el estudio de cartas a la derecha, y la placa del efecto de colección en la esquina con «+10 % de probabilidad de reclutamiento».](assets/skills-panel.jpg)

- Las **pestañas** filtran por tipo — **Pasiva**, **Activa**, **De personaje** — y la fila de rarezas por rareza, de **Común** a **Legendaria**; el buscador y la lista de orden reducen la cuadrícula.
- **El color del marco y los rombos** muestran la rareza de la carta.
- **Haz clic en una carta** para verla en el estudio de cartas: imagen, distintivos de rareza y tipo, descripción, sus efectos y, si otorga una, la carta de acción que da.
- El **efecto de colección** suma los efectos de todas las habilidades reunidas: dos habilidades de +10 % y +5 % de reclutamiento muestran +15 %.

La carta del Tratante de esclavos aún no tiene imagen: el juego nombra `characters/skills/slave_master` como su imagen, pero ningún mod incluye ese archivo, así que el marco queda vacío.

## Una definición de habilidad

Las habilidades son archivos JSON de tipo `skill`, en cualquier parte de un mod. Este es el Tratante de esclavos, de Cthangover; compáralo con el panel de arriba:

```json
{
  "Kind": "skill",
  "Items": [
    {
      "Id": "slave_master",
      "Name": "characters/skills/slave_master_name",
      "Description": "characters/skills/slave_master",
      "Image": "characters/skills/slave_master",
      "Type": 1,
      "Rare": 3,
      "Effects": [
        { "Key": "characters/skills/effects/recruit", "Value": 10, "Percent": true }
      ]
    }
  ]
}
```

- `Name`, `Description` — claves de traducción.
- `Image` — la imagen de la carta.
- `Type` — `Active` (0), `Passive` (1) o `ForCharacter` (2); valen tanto nombres como números. El Tratante de esclavos es **pasivo**.
- `Rare` — `Common` (0), `Uncommon`, `Rare`, `Epic` (3), `Legendary`. El Tratante de esclavos es **épico**.
- `Effects` — números para el efecto de colección: `Key` es la clave de traducción de lo que se mejora, `Value` la cantidad y `Percent` si se muestra con %. Una misma clave debe mantener la misma unidad en todas las habilidades, porque los valores se suman.
- `GrantsAction` (opcional) — el id de una carta de acción (véase Characters Engine). Al obtener la habilidad, esa carta entra también en la reserva de acciones del panel Acciones de personaje.

## Qué hace una habilidad en el juego

El panel solo muestra la habilidad. Lo que cambia lo deciden los mods que la comprueban:

- Recruiting ofrece **esclavizar** a un enemigo derrotado solo mientras el jugador tenga `slave_master`. La probabilidad de éxito es el `RecruitmentChance` del propio enemigo; el +10 % de la carta se ve en el panel, pero recruiting no lo suma.
- Una habilidad con `GrantsAction` entrega su carta de acción, que el jugador coloca después en el hueco de un personaje.

## Dar una habilidad desde un escenario

```scenario
action character.add_skill skill_id=slave_master
```

Dar una habilidad dos veces no hace nada la segunda, así que una escena repetida no puede duplicarla a ella ni a su carta de acción. Cthangover da el Tratante de esclavos al empezar una partida nueva.

## Si algo falla

1. **La habilidad no aparece.** El `skill_id` debe coincidir con el `Id` de la habilidad; un id desconocido se escribe en el registro como advertencia.
2. **La carta no tiene imagen.** Ningún mod incluye una imagen con el nombre puesto en `Image`.
3. **El efecto de colección muestra un total extraño.** Dos habilidades usan la misma clave de efecto con valores de `Percent` distintos.

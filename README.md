# characterchat-embed

Lokales Bedeutungsmodell (Text-Embeddings) für die CharacterChat-App. Die App lädt es beim ersten Start herunter und rechnet damit auf dem Gerät,
ohne Cloud-Dienst.

## Herkunft

Abgeleitet von [Snowflake/snowflake-arctic-embed-l-v2.0](https://huggingface.co/Snowflake/snowflake-arctic-embed-l-v2.0), Fassung
`ac6544c8a46e00af67e330e85a9028c66b8cfd9a`, unter der Apache-Lizenz 2.0 (siehe [LICENSE](LICENSE)).

## Änderungen gegenüber dem Original

- Nachtrainiert mit rund 3 800 deutschen Tripeln (Anker, passender Text, schwieriges Gegenbeispiel) aus erfundenen Rollenspielfiguren:
  Nachricht ↔ Profileintrag, Frage ↔ Erinnerung, Nennung ↔ Gemeintes, gleiche Aussage anders gesagt, Geste ↔ Szenenbeschreibung.
  Verlust MultipleNegativesRankingLoss (zwischengespeicherte Form), 1 Epoche, Lernrate 2e-5, die Wortliste blieb fest.
- Nach ONNX exportiert (Eingänge `input_ids`, `attention_mask`; Ausgang `last_hidden_state`) und dynamisch auf int8 verkleinert, je Ausgangskanal.
- `tokenizer.json` ist unverändert die Datei des Originals.

Nutzung wie beim Original: Suchtexte bekommen das Präfix `query: `, abgelegte Texte keins; der Vektor ist der erste Token (CLS), normiert.

## Dateien (Release v1)

| Datei | Größe (Bytes) | SHA-256 |
|---|---|---|
| `tokenizer.json` | 17 083 074 | `39feb9863a378165ab9c5c689047203d789422966c0c58721c5309fd039a8edc` |
| `model_int8.onnx` | 569 722 157 | `5be35410c0907c2ec472fa1135ed3e03e71dbe1763dae16c24f98a341279a543` |

## Messung

AUC an 90 beschrifteten deutschen Paaren aus der App (wie oft verwandte Paare ähnlicher sind als unverwandte), int8 im App-Baustein:

| | je Aufgabe | über alles |
|---|---|---|
| OpenAI text-embedding-3-small | 0,965 | 0,935 |
| Original (int8) | 0,974 | – |
| dieses Modell (int8) | 0,993 | 0,960 |

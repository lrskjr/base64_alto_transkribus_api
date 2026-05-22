# base64_alto_transkribus_api


Notebook: Python_script_recognize_base64_alto_v4.ipynb

Scriptet behandler jpg-filer i stregkodemapper under scriptets rodmappe. For hver jpg oprettes ALTO (.xml) i transkribus_alto_filer/ og tekst (.txt) i transkribus_txt_filer/.

## Genkørsel hvis der mangler filer

Hvis der efter en kørsel mangler alto- eller txt-filer, kan scriptet genkøres på samme mapper.

Ved genkørsel springes billeder over, der allerede er færdige. Et billede betragtes som færdigt, når både .xml og .txt findes og har indhold (størrelse > 0 byte).

Tjek før hver jpg:

- xml_ready = filen findes og er ikke tom
- txt_ready = filen findes og er ikke tom
- begge sande -> spring over (log: Genoptagelse: springer færdig fil over)

### Ved genkørsel på samme mapper

| Situation efter 1. kørsel | Ved 2. kørsel |
|---------------------------|---------------|
| Succes (xml + txt) | Springes over |
| Netværksfejl eller ingen output | Behandles igen (ny API-kørsel) |
| Kun .xml eller kun .txt | Behandles igen (kræver begge) |
| Tom .xml eller .txt (0 bytes) | Behandles igen |

## Netværksfejl under kørsel (v2.4)

Ved forbindelsesfejl til transkribus.eu (f.eks. timeout, Max retries exceeded) i samme kørsel:

- Scriptet venter 60 sekunder (NETWORK_RETRY_WAIT)
- Logger: Transkribus utilgængelig, venter 60s ...
- State sættes til WAITING_TRANSCRIBUS
- Samme jpg forsøges igen — ikke næste jpg i rækken
- Eksisterende process_id genbruges efter oprettelse (poll fortsætter på samme job)

HTTP-fejl ved oprettelse, process FAILED hos Transkribus, eller tomt ALTO-svar: filen markeres som failed, og batch går videre til næste jpg.

## Filer ved kørsel

- Log: transkribus_batch_YYYYMMDD_HHMMSS.log (i scriptmappen)
- State: transkribus_batch_state.json (genoptagelse og tællere)

## Justering

I script-cellen kan du ændre:

- NETWORK_RETRY_WAIT = 60 (sekunder mellem forsøg ved nedetid)
- REQUEST_TIMEOUT = (10, 120) (connect- og read-timeout for API-kald)

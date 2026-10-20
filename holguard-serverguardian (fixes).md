# Checklist di Correzione per la PR #5372 (Server Guardian / `hol-guard`)

## 1. Problema: Matcher generici per `apt` e `docker`
* **Cosa dicono i bot:** I matcher attuali basati su `arguments.v1` intercettano qualsiasi comando che contenga i token chiave (es. `run`, `upgrade`, `pull`) ovunque nell'argv, rischiando di bloccare comandi non correlati (es. `docker exec web npm run build`)[cite: 3].
* **Cosa fare (`contributions/command-sources/command.serverguardian.json`):**
  * Perfezionare i matcher delle regole di sistema e Docker affinché puntino in modo più specifico ai sottoinsiemi corretti (es. `apt update`, `apt upgrade`, `apt full-upgrade`, `apt dist-upgrade`, `docker pull`, `docker container run/create`, `docker image pull`, e `docker-compose up`)[cite: 3].
  * Evitare che parole chiave isolate in comandi di sola lettura (come `docker inspect up` o `apt show upgrade`) attivino i blocchi di revisione[cite: 3].

---

## 2. Problema: Regole di Telemetria disabilitate nei Test
* **Cosa dicono i bot:** Le regole di telemetria e notifiche (`mailer.py`) usano `default_mode: "disabled"` e i test nei fixture attuali eseguono i casi con `enabled_extensions: []`, verificando di fatto solo il comportamento di fallback e non testando l'effettivo percorso "free" ad estensione attiva[cite: 3].
* **Cosa fare (`contributions/command-sources/command.serverguardian.json` e `tests/fixtures/command-source-serverguardian.v1.json`):**
  * Aggiornare i fixture di test (`telemetry-free`, `mailer-free`, `linux-mailer-free`) affinché abilitino esplicitamente l'estensione (`enabled_extensions: ["command.serverguardian"]`)[cite: 3].
  * Impostare l'azione attesa su `"allow"` e configurare correttamente gli `expected_effective_segments` a `[0]` per confermare che i comandi di telemetria passino liberamente come previsto[cite: 3].
  * Restringere il matcher del mailer per evitare che qualsiasi script generico chiamato `mailer.py` in altri progetti venga intercettato[cite: 3].

---

## 3. Piano d'Azione Operativo
1. Modificare il file `contributions/command-sources/command.serverguardian.json` affinando i matcher.
2. Aggiornare il file `tests/fixtures/command-source-serverguardian.v1.json` attivando l'estensione nei test di telemetria.
3. Effettuare il `git add`, `git commit` e `git push` sul branch della tua PR per aggiornare automaticamente la richiesta su GitHub.
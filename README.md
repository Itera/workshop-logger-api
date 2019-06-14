# Logger API
> Programmeringsspråk: valgfritt

## Oppgaver

### 1. Lag en logger
> Ta utgangspunkt i eksempelkoden under

#### 1a)

Optimaliser eksempelkoden med utgangspunkt i [SOLID]-prinsippene for objektorientert programmering.
Fokuser på `Single responsibility principle` (en klasse/metode skal gjøre en ting), og
`Open–closed principle` (det skal være enkelt å legge til en ny logger).

#### 1b)

Legg til følgende funksjoner:
- Logglinjer skal minimum inneholde dato/tids-stempel, logg-nivå, og logg-teksten
- Logg til fil, konsoll, og en annen valgfri logger
    * På fil-loggeren må det være funksjonalitet for å skrive til ny fil dersom en fil blir for lang.
      I denne oppgaven vil det si fem linjer. Du skal da få: `log-<DATO>-0.log`, `log-<DATO>-1.log`, osv.

### 2. Legg til et HTTP API

Vi ønsker et API med følgende endepunkter:

| Method | Endpoint           | Request body                                    | Funksjon                 |
|--------|--------------------|-------------------------------------------------|--------------------------|
| POST   | `/api/log`         | `{ text: "some log line", level: <LOG_LEVEL> }` | Legg til logglinje       |
| GET    | `/api/log`         | -                                               | Hent 10 siste logglinjer |
| GET    | `/api/log?lines=X` | -                                               | Hent X siste logglinjer  |

Logg-nivåer:
* TRACE
* DEBUG
* INFO
* WARN
* ERROR
* FATAL

## Eksempelkode

`Application.java`
```java
public class Application {
  public static void main(String[] args) {
    LogEngine logEngine = new LogEngine();

    logEngine.writeLogMessage("Hello");
    logEngine.writeLogMessage("World");
  }
}
```

`LogEngine.java`
```java
public class LogEngine {
  private static final FileLogger FILE_LOGGER = new FileLogger();
  private static final ConsoleLogger CONSOLE_LOGGER = new ConsoleLogger();

  public void writeLogMessage(String message) {
    CONSOLE_LOGGER.writeToConsole(message + "\n");
    try {
      FILE_LOGGER.writeToFile(message + "\n");
    } catch (Exception e) {
      // NOP
    }
  }
}
```

`FileLogger.java`
```java
public class FileLogger {
  private static final String LOG_FILE_NAME = "log.txt";

  public void writeToFile(String message) throws Exception {

    Path filePath = Paths.get(LOG_FILE_NAME);

    if (filePath.toFile().exists()) {
      Files.write(
          filePath,
          String.format("%s %s", new Date().toString(), message).getBytes(),
          StandardOpenOption.APPEND
      );
    } else {
      Files.write(
          filePath,
          String.format("%s %s", new Date().toString(), message).getBytes(),
          StandardOpenOption.CREATE
      );
    }
  }
}
```

`ConsoleLogger.java`
```java
public class ConsoleLogger {
  public void writeToConsole(String message) {
    System.out.format("%s %s", new Date().toString(), message);
  }
}
```



[SOLID]: https://en.wikipedia.org/wiki/SOLID

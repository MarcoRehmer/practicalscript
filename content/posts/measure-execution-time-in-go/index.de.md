---
title: "Ausführungszeit in Go messen"
date: 2024-06-22T13:00:00Z
aliases: ["/meassure-execution-time-in-go"]
tags: ["Go", "Metrics"]
author: "Marco Rehmer"
draft: false
summary: Finde heraus, wie lange deine Funktionen zur Ausführungszeit in Go laufen
cover:
  image: "images/golang-logo.png"
  alt: "Golang Logo"
---

Du hast Probleme herauszufinden, wie lange deine Funktionen in Go laufen? Oder willst einfach nur sehen, wie viel Zeit eine Abfrage in Anspruch genommen hat?

Ich auch! Aber keine Angst, ich werde dir zeigen, wie du das Problem auf eine sehr kompakte und saubere Weise lösen kannst.

> 💡 Am Ende dieses Beitrags findest du diese Anleitung auf YouTube

## TL;DR

Für alle eiligen unter euch gibt es hier das Code Snippet, mit dem ihr eure Ausführungszeit verfolgen könnt:

```go
    func main() {
      someHeavyCalc(2)
      fmt.Println("done.")
    }

    func timer(name string) func() {
      start := time.Now()
      return func() {
          fmt.Printf("[%s]: %s\n", name, time.Since(start))
      }
    }

    func someHeavyCalc(duration time.Duration) {
      defer timer("someHeavyCalc")()
      time.Sleep(time.Second * duration)
    }
```

## Timer Funktion

Unsere Timer-Funktion nimmt einen Namen als Argument, damit wir sehen können, welcher Kontext ausgegeben wird. Als Nächstes werden wir den Startpunkt mit `time.Now()` erfassen, um den aktuellen Zeitstempel zu erhalten.

```go
    func timer(name string) func() {
      start := time.Now()
      // ...
    }
```

Unsere Funktion wird eine **neue** anonyme Funktion zurückgeben, die die Zeit ausgibt, die unser Prozess gebraucht hat.

```go
    func timer(name string) func() {
      start := time.Now()
        return func() {
          fmt.Printf("[%s]: %s\n", name, time.Since(start))
        }
      }
```

## Die Zeit messen

Okay, jetzt können wir unsere Funktion verwenden, um eine unserer Funktionen zu verfolgen.

Verwende einfach die Timer-Funktion am Anfang einer Ihrer Funktionen (oder wo auch immer du deine Messung starten willst) wie folgt:

```go
defer timer("myFunctionName")()
```

Du kannst hier zwei kleine Dinge bemerken:

1. nachdem wir unsere reguläre Funktion aufgerufen haben, fügen wir ein weiteres Paar von Klammern `()` an, um **die zurückgegebene anonyme Funktion** aufzurufen
2. wir verwenden das Schlüsselwort `defer`, um den Aufruf unserer anonymen Funktion am Ende der übergeordneten Funktion zu _verzögern_

Mit `defer` plant Go die angegebene Funktion am Ende der Wrapping-Funktion ein, und führt sie aus, nachdem go die letzte Zeile der Elternfunktion erreicht hat. Das bedeutet, dass die Funktion `fmt.Printf(...)` erst ausgeführt wird, **nachdem** alles fertig ist und die korrekte Zeitspanne ausgeben werden kann.

Einfach, oder?

## Sieh dir den Beitrag auf Youtube an

{{< youtube ThzXpXDJS2M >}}

<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Technik-Schätzfragen</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #f0f0f0;
            margin: 0;
            padding: 20px;
            display: flex;
            flex-direction: column;
            min-height: 100vh;
        }
        .header {
            display: flex;
            align-items: center;
            justify-content: center;
            margin-bottom: 20px;
        }
        h1 {
            color: #333;
            margin-right: 10px;
        }
        .icon {
            font-size: 24px;
        }
        .main-container {
            flex-grow: 1;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        .quiz-container {
            background-color: white;
            border-radius: 10px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
            padding: 30px;
            text-align: center;
            max-width: 600px;
            width: 90%;
            position: relative;
        }
        #question, #answer {
            margin-bottom: 20px;
            font-size: 18px;
            line-height: 1.5;
        }
        #answer {
            font-weight: bold;
            color: #4a4a4a;
        }
        .button-group {
            display: flex;
            justify-content: center;
            gap: 10px;
            margin-top: 20px;
        }
        button {
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
            border: none;
            border-radius: 5px;
            transition: background-color 0.3s, transform 0.1s;
            box-shadow: 0 2px 4px rgba(0,0,0,0.2);
        }
        button:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 8px rgba(0,0,0,0.2);
        }
        #prevBtn, #nextBtn {
            background-color: #4CAF50;
            color: white;
        }
        #showAnswerBtn {
            background-color: #008CBA;
            color: white;
        }
        #remainingQuestions {
            position: absolute;
            top: 10px;
            right: 10px;
            font-size: 14px;
            color: #666;
        }
        .id-container {
            position: fixed;
            bottom: 20px;
            right: 20px;
            background-color: white;
            padding: 10px;
            border-radius: 5px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        #quizIdInput, #gameIdInput {
            font-size: 14px;
            padding: 5px;
            width: 80px;
            margin-right: 5px;
        }
        #changeIdBtn {
            font-size: 12px;
            padding: 5px 10px;
            background-color: #f44336;
            color: white;
        }
        .theme-icon {
            font-size: 48px;
            margin-bottom: 20px;
        }
        #hostSelection, #gameIdInput {
            margin-bottom: 10px;
        }
        #answerInput {
            font-size: 14px;
            padding: 5px;
            width: 100px;
            margin-right: 5px;
        }
        #unitSelect {
            font-size: 14px;
            padding: 5px;
        }
        #checkAnswerBtn {
            background-color: #FFA500;
            color: white;
        }
        #animation {
            width: 100px;
            height: 100px;
            margin: 0 auto 20px;
            border: 5px solid #4CAF50;
            border-radius: 50%;
            border-top: 5px solid #FFA500;
            animation: spin 1s linear infinite;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
    </style>
</head>
<body>
    <div class="header">
        <h1>Technik-Schätzfragen</h1>
        <span class="icon">🔧</span>
    </div>
    <div class="main-container">
        <div class="quiz-container">
	<div id="gameInfo" style="display: none;">
    <p>Game-ID: <span id="currentGameId"></span></p>
    <p>Quiz-ID: <span id="currentQuizId"></span></p>
    <p>Ihr Punktestand: <span id="playerScore">0</span></p>
    <p>Gegner Punktestand: <span id="opponentScore">0</span></p>
</div>
            <div id="animation"></div>
            <div id="hostSelection">
                <p>Sind Sie der Host?</p>
                <button onclick="selectHost(true)">Ja</button>
                <button onclick="selectHost(false)">Nein</button>
            </div>
            <div id="gameIdInput" style="display: none;">
                <input type="text" id="gameIdInputField" placeholder="Game-ID">
                <button onclick="joinGame()">Spiel beitreten</button>
            </div>
            <div id="themeIcon" class="theme-icon"></div>
            <div id="remainingQuestions"></div>
            <div id="question"></div>
            <div id="answerSection" style="display: none;">
 		   <input type="number" id="answerInput" placeholder="Ihre Antwort">
		    <select id="unitSelect"></select>
		    <button id="submitAnswerBtn">Antwort einreichen</button>
 		   <button id="checkAnswerBtn" style="display: none;">Antworten überprüfen</button>
		</div>
                <input type="number" id="answerInput" placeholder="Ihre Antwort">
                <select id="unitSelect"></select>
                <button id="checkAnswerBtn" onclick="checkAnswer()">Antwort prüfen</button>
            </div>
            <div id="answer" style="display: none;"></div>
            <div class="button-group">
                <button id="prevBtn" onclick="previousQuestion()" style="display: none;">Vorherige Frage</button>
                <button id="showAnswerBtn" onclick="showAnswer()">Antwort anzeigen</button>
                <button id="nextBtn" onclick="nextQuestion()" style="display: none;">Nächste Frage</button>
            </div>
        </div>
    </div>
    <div class="id-container">
        <input type="text" id="quizIdInput" placeholder="Quiz-ID" maxlength="4">
        <button id="changeIdBtn" onclick="changeQuizId()">ID ändern</button>
        <div id="gameIdDisplay" style="display: none;"></div>
    </div>

    <script>
        const quizSets = {
            "ET01": { topic: "Elektrotechnik", difficulty: "Leicht", icon: "⚡" },
            "ET02": { topic: "Elektrotechnik", difficulty: "Schwer", icon: "⚡" },
            "BT01": { topic: "Bautechnik", difficulty: "Leicht", icon: "🏗️" },
            "BT02": { topic: "Bautechnik", difficulty: "Schwer", icon: "🏗️" },
            "EVT1": { topic: "Entsorgungs- und Versorgungstechnik", difficulty: "Leicht", icon: "♻️" },
            "EVT2": { topic: "Entsorgungs- und Versorgungstechnik", difficulty: "Schwer", icon: "♻️" },
            "IT01": { topic: "Informationstechnik", difficulty: "Leicht", icon: "💻" },
            "IT02": { topic: "Informationstechnik", difficulty: "Schwer", icon: "💻" },
        };

        const questions = {
            "ET01": [
                { q: "Wie hoch ist die Spannung in einer typischen Haushaltssteckdose in Deutschland?", a: 230, unit: "Volt" },
                { q: "Wie viele Ampere hat eine Standard-Sicherung in einem Haushalt?", a: 16, unit: "Ampere" },
                { q: "Wie viel Watt verbraucht eine durchschnittliche LED-Glühbirne?", a: 8.5, unit: "Watt" },
                { q: "Wie lang ist ein typisches Stromkabel für Haushaltsgeräte?", a: 1.5, unit: "Meter" },
{ q: "Wie viel Strom verbraucht ein durchschnittlicher Kühlschrank pro Jahr?", a: 150, unit: "Kilowattstunden" },
        { q: "Wie viele Volt hat eine typische AA-Batterie?", a: 1.5, unit: "Volt" },
        { q: "Wie viel Watt hat eine durchschnittliche Mikrowelle?", a: 800, unit: "Watt" },
        { q: "Wie viele Ampere kann ein USB-Anschluss typischerweise liefern?", a: 0.5, unit: "Ampere" },
        { q: "Wie viel Energie speichert eine typische Powerbank?", a: 10000, unit: "Milliamperestunden" },
        { q: "Wie viele Watt verbraucht ein durchschnittlicher Laptop?", a: 50, unit: "Watt" },
        { q: "Wie viele Volt hat ein Autobatterie?", a: 12, unit: "Volt" },
        { q: "Wie viel Strom verbraucht eine Waschmaschine pro Waschgang durchschnittlich?", a: 1, unit: "Kilowattstunde" },
        { q: "Wie viele Watt hat eine typische Stereoanlage?", a: 100, unit: "Watt" },
        { q: "Wie viel Strom verbraucht ein Fernseher pro Stunde im Durchschnitt?", a: 0.1, unit: "Kilowattstunden" },
        { q: "Wie viele Ampere hat ein typischer Haartrockner?", a: 10, unit: "Ampere" },
        { q: "Wie viel Energie verbraucht ein Smartphone beim einmaligen vollständigen Aufladen?", a: 0.01, unit: "Kilowattstunden" },
            ],

    "ET02": [ // Elektrotechnik Schwer
        { q: "Wie hoch ist die Spannung in einem Hochspannungsnetz zur Stromübertragung?", a: 380000, unit: "Volt" },
        { q: "Wie viele Ampere fließen typischerweise durch einen Blitzableiter bei einem Blitzeinschlag?", a: 30000, unit: "Ampere" },
        { q: "Wie viele Watt Leistung hat ein großes Windrad?", a: 5000000, unit: "Watt" },
        { q: "Wie hoch ist der Wirkungsgrad eines modernen Solarpanels?", a: 22, unit: "Prozent" },
        { q: "Wie viele Volt hat eine Zelle in einer Lithium-Ionen-Batterie?", a: 3.7, unit: "Volt" },
        { q: "Wie viele Ampere fließen durch eine 100-Watt-Glühbirne bei 230 Volt?", a: 0.43, unit: "Ampere" },
        { q: "Wie viele Kilovoltampere (kVA) hat ein typischer Transformator in einer Ortsnetzstation?", a: 630, unit: "Kilovoltampere" },
        { q: "Wie viele Milliohm Widerstand hat ein Meter Kupferdraht mit 1mm² Querschnitt?", a: 17.2, unit: "Milliohm" },
        { q: "Wie viele Mikrofarad Kapazität hat ein typischer Entstörkondensator in einem Netzteil?", a: 0.1, unit: "Mikrofarad" },
        { q: "Wie viele Henry Induktivität hat eine typische Drossel in einem Schaltnetzteil?", a: 0.001, unit: "Henry" },
        { q: "Wie viele Elektronenvolt Energie benötigt man, um ein Elektron aus einem Kupferatom zu lösen?", a: 7.7, unit: "Elektronenvolt" },
        { q: "Wie viele Tesla magnetische Flussdichte erzeugt ein starker Neodym-Magnet?", a: 1.3, unit: "Tesla" },
        { q: "Wie viele Hertz hat die Taktfrequenz eines modernen Mikroprozessors?", a: 3500000000, unit: "Hertz" },
        { q: "Wie viele Watt pro Quadratmeter Strahlungsleistung sendet die Sonne zur Erde?", a: 1361, unit: "Watt pro Quadratmeter" },
        { q: "Wie viele Coulomb elektrische Ladung fließen durch einen 1-Ampere-Strom in einer Sekunde?", a: 1, unit: "Coulomb" },
        { q: "Wie viele Ohm Widerstand hat ein typisches Multimeter im Strommessmodus?", a: 0.1, unit: "Ohm" },
    ],

    "BT01": [ // Bautechnik Leicht
        { q: "Wie hoch ist ein durchschnittliches Einfamilienhaus in Deutschland?", a: 7.5, unit: "Meter" },
        { q: "Wie dick ist eine Standard-Innenwand in einem modernen Haus?", a: 11.5, unit: "Zentimeter" },
        { q: "Wie viele Ziegel werden für 1 m² Mauer benötigt?", a: 55, unit: "Stück" },
        { q: "Wie viel wiegt ein Kubikmeter Beton?", a: 2400, unit: "Kilogramm" },
        { q: "Wie lang ist eine Standard-Europalette?", a: 120, unit: "Zentimeter" },
        { q: "Wie hoch ist eine typische Zimmerdecke in einem Wohnhaus?", a: 250, unit: "Zentimeter" },
        { q: "Wie breit ist eine normale Haustür?", a: 90, unit: "Zentimeter" },
        { q: "Wie viele Liter Farbe benötigt man für 10 m² Wandfläche?", a: 1, unit: "Liter" },
        { q: "Wie viele Kilogramm wiegt ein Standard-Ziegelstein?", a: 2.5, unit: "Kilogramm" },
        { q: "Wie dick ist eine typische Dämmschicht im Dach?", a: 20, unit: "Zentimeter" },
        { q: "Wie viele Quadratmeter Wohnfläche hat eine durchschnittliche 3-Zimmer-Wohnung?", a: 75, unit: "Quadratmeter" },
        { q: "Wie viele Stufen hat eine typische Treppe zwischen zwei Stockwerken?", a: 16, unit: "Stufen" },
        { q: "Wie hoch ist ein Standard-Fensterbankett?", a: 80, unit: "Zentimeter" },
        { q: "Wie viele Liter Wasser enthält ein Kubikmeter Frischbeton?", a: 150, unit: "Liter" },
        { q: "Wie viele Quadratmeter deckt ein 25-kg-Sack Fliesenkleber ab?", a: 5, unit: "Quadratmeter" },
        { q: "Wie lang ist ein Standard-Gipskartonplatte?", a: 250, unit: "Zentimeter" },
    ],

    "BT02": [ // Bautechnik Schwer
        { q: "Wie hoch ist der höchste Wolkenkratzer der Welt (Burj Khalifa)?", a: 828, unit: "Meter" },
        { q: "Wie lang ist die längste Brücke der Welt (Danyang–Kunshan Grand Bridge)?", a: 164800, unit: "Meter" },
        { q: "Wie viele Tonnen Stahl wurden für den Eiffelturm verwendet?", a: 7300, unit: "Tonnen" },
        { q: "Wie tief ist das tiefste Bohrloch der Welt (Kola-Bohrung)?", a: 12262, unit: "Meter" },
        { q: "Wie viele Kubikmeter Beton wurden für den Drei-Schluchten-Damm in China verwendet?", a: 27200000, unit: "Kubikmeter" },
        { q: "Wie dick ist die dickste Betonmauer der Welt (Grand Coulee Dam)?", a: 150, unit: "Meter" },
        { q: "Wie viele Tonnen wiegt die schwerste von Menschen bewegte Steinstruktur (Ramses-II-Statue)?", a: 1000, unit: "Tonnen" },
        { q: "Wie hoch ist der höchste Damm der Welt (Jinping-I-Talsperre)?", a: 305, unit: "Meter" },
        { q: "Wie lang ist der längste Tunnel der Welt (Gotthard-Basistunnel)?", a: 57100, unit: "Meter" },
        { q: "Wie viele Quadratmeter Fläche hat das größte Gebäude der Welt (New Century Global Center)?", a: 1760000, unit: "Quadratmeter" },
        { q: "Wie viele Tonnen wiegt die schwerste Glocke der Welt (Zar-Kolokol)?", a: 201, unit: "Tonnen" },
        { q: "Wie viele Meter pro Sekunde fährt der schnellste Aufzug der Welt (Shanghai Tower)?", a: 20.5, unit: "Meter pro Sekunde" },
        { q: "Wie viele Grad Celsius hält der feuerfesteste Beton der Welt stand?", a: 1850, unit: "Grad Celsius" },
        { q: "Wie viele Pascal Druck hält der druckfesteste Beton der Welt aus?", a: 720000000, unit: "Pascal" },
        { q: "Wie viele Newtonmeter Drehmoment kann die größte Schraube der Welt (für Windkraftanlagen) aufnehmen?", a: 10000000, unit: "Newtonmeter" },
        { q: "Wie viele Quadratmeter Glas wurden für die Fassade des Burj Khalifa verwendet?", a: 142000, unit: "Quadratmeter" },
    ],
 "EVT1": [ // Entsorgungs- und Versorgungstechnik Leicht
        { q: "Wie viel Liter Wasser verbraucht eine durchschnittliche Toilettenspülung?", a: 7.5, unit: "Liter" },
        { q: "Wie hoch ist der durchschnittliche Wasserverbrauch pro Person pro Tag in Deutschland?", a: 120, unit: "Liter" },
        { q: "Wie viel Müll produziert ein Deutscher durchschnittlich pro Jahr?", a: 450, unit: "Kilogramm" },
        { q: "Wie viel Prozent des Hausmülls werden in Deutschland recycelt?", a: 67, unit: "Prozent" },
        { q: "Wie viele Liter Wasser verbraucht eine Waschmaschine pro Waschgang durchschnittlich?", a: 45, unit: "Liter" },
        { q: "Wie viele Kilogramm Papier verbraucht ein Deutscher durchschnittlich pro Jahr?", a: 220, unit: "Kilogramm" },
        { q: "Wie viele Liter Wasser werden für die Produktion einer Jeans benötigt?", a: 8000, unit: "Liter" },
        { q: "Wie viele Kilowattstunden Strom verbraucht ein durchschnittlicher 2-Personen-Haushalt pro Jahr?", a: 3000, unit: "Kilowattstunden" },
        { q: "Wie viele Liter Wasser verdunstet ein Mensch durchschnittlich pro Tag?", a: 2.5, unit: "Liter" },
        { q: "Wie viele Kilogramm Lebensmittel wirft ein Deutscher durchschnittlich pro Jahr weg?", a: 75, unit: "Kilogramm" },
        { q: "Wie viele Liter Wasser verbraucht man durchschnittlich für eine 5-minütige Dusche?", a: 60, unit: "Liter" },
        { q: "Wie viele Gramm Plastikmüll produziert ein Deutscher durchschnittlich pro Tag?", a: 110, unit: "Gramm" },
        { q: "Wie viele Liter Wasser werden für die Produktion eines Kilogramms Rindfleisch benötigt?", a: 15000, unit: "Liter" },
        { q: "Wie viele Kilogramm Elektroschrott produziert ein Deutscher durchschnittlich pro Jahr?", a: 20, unit: "Kilogramm" },
        { q: "Wie viele Liter Wasser verbraucht ein Geschirrspüler pro Spülgang durchschnittlich?", a: 12, unit: "Liter" },
        { q: "Wie viele Kilogramm CO2 produziert ein durchschnittlicher PKW pro 100 km Fahrt?", a: 12, unit: "Kilogramm" },
    ],
    "EVT2": [ // Entsorgungs- und Versorgungstechnik Schwer
        { q: "Wie viele Kubikmeter Erdgas verbraucht Deutschland pro Jahr?", a: 95000000000, unit: "Kubikmeter" },
        { q: "Wie viele Tonnen Müll werden weltweit pro Jahr produziert?", a: 2010000000, unit: "Tonnen" },
        { q: "Wie viele Liter Wasser werden weltweit pro Tag für die Industrie verbraucht?", a: 770000000000, unit: "Liter" },
        { q: "Wie viele Kilowattstunden Strom erzeugt das größte Solarkraftwerk der Welt pro Jahr?", a: 1177000000, unit: "Kilowattstunden" },
        { q: "Wie viele Tonnen Plastik landen jährlich in den Weltmeeren?", a: 8000000, unit: "Tonnen" },
        { q: "Wie viele Kubikmeter Wasser fasst der größte Staudamm der Welt (Drei-Schluchten-Damm)?", a: 39300000000, unit: "Kubikmeter" },
        { q: "Wie viele Kilometer lang ist das längste Unterwasser-Stromkabel der Welt?", a: 720, unit: "Kilometer" },
        { q: "Wie viele Tonnen Elektroschrott werden weltweit pro Jahr produziert?", a: 50000000, unit: "Tonnen" },
        { q: "Wie viele Liter Öl gelangen jährlich durch Unfälle ins Meer?", a: 1500000000, unit: "Liter" },
        { q: "Wie viele Quadratkilometer Regenwald werden pro Jahr abgeholzt?", a: 150000, unit: "Quadratkilometer" },
        { q: "Wie viele Tonnen Kohlendioxid speichert ein Hektar Regenwald pro Jahr?", a: 10, unit: "Tonnen" },
        { q: "Wie viele Meter tief liegt die tiefste Erdöl-Bohrung der Welt?", a: 12289, unit: "Meter" },
        { q: "Wie viele Kilogramm Uran werden jährlich weltweit abgebaut?", a: 54000000, unit: "Kilogramm" },
        { q: "Wie viele Liter Wasser werden für die Produktion eines Mikrochips benötigt?", a: 32, unit: "Liter" },
        { q: "Wie viele Tonnen Lebensmittel werden in Deutschland pro Jahr weggeworfen?", a: 12000000, unit: "Tonnen" },
        { q: "Wie viele Kilometer lang ist das längste Fernwärmenetz der Welt (in Kopenhagen)?", a: 167, unit: "Kilometer" },
    ],

      "IT01": [ // Informationstechnik Leicht
        { q: "Wie viele Bits sind in einem Byte?", a: 8, unit: "Bits" },
        { q: "Wie viel Speicherplatz hat eine typische DVD?", a: 4.7, unit: "Gigabyte" },
        { q: "Wie schnell ist eine durchschnittliche Internetverbindung in Deutschland?", a: 100, unit: "Mbit/s" },
        { q: "Wie viele Zeichen kann eine SMS maximal enthalten?", a: 160, unit: "Zeichen" },
        { q: "Wie viele Gigabyte Speicherplatz hat ein typisches Smartphone?", a: 128, unit: "Gigabyte" },
        { q: "Wie viele Megapixel hat die Hauptkamera eines durchschnittlichen Smartphones?", a: 12, unit: "Megapixel" },
        { q: "Wie viele Stunden Akkulaufzeit hat ein typisches Laptop?", a: 8, unit: "Stunden" },
        { q: "Wie viele Tasten hat eine Standard-QWERTZ-Tastatur?", a: 105, unit: "Tasten" },
        { q: "Wie viele Gigahertz Taktfrequenz hat ein durchschnittlicher Laptop-Prozessor?", a: 2.4, unit: "Gigahertz" },
        { q: "Wie viele Watt Leistung hat ein typisches Laptop-Netzteil?", a: 65, unit: "Watt" },
        { q: "Wie viele Millimeter dick ist ein modernes Smartphone?", a: 8, unit: "Millimeter" },
        { q: "Wie viele Gramm wiegt ein durchschnittliches Tablet?", a: 450, unit: "Gramm" },
        { q: "Wie viele Frames pro Sekunde werden bei den meisten Filmen verwendet?", a: 24, unit: "Frames pro Sekunde" },
        { q: "Wie viele Megabyte groß ist eine typische Musik-MP3-Datei?", a: 4, unit: "Megabyte" },
        { q: "Wie viele Jahre beträgt die durchschnittliche Lebensdauer eines Laptops?", a: 5, unit: "Jahre" },
        { q: "Wie viele Anschlüsse hat ein typischer USB-Hub?", a: 4, unit: "Anschlüsse" },
    ],

    "IT02": [ // Informationstechnik Schwer
        { q: "Wie viele Transistoren enthält ein moderner High-End-Prozessor?", a: 30000000000, unit: "Transistoren" },
        { q: "Wie viele Petabyte Daten werden täglich im Internet generiert?", a: 2500, unit: "Petabyte" },
        { q: "Wie viele Kilometer lang ist das längste Unterseekabel der Welt?", a: 39000, unit: "Kilometer" },
        { q: "Wie viele Gigabit pro Sekunde erreicht der schnellste kommerzielle Internetanschluss?", a: 10000, unit: "Gigabit pro Sekunde" },
        { q: "Wie viele Watt Leistung verbraucht ein typisches Rechenzentrum?", a: 5000000, unit: "Watt" },
        { q: "Wie viele Exabyte betrug das globale IP-Verkehrsvolumen im Jahr 2021?", a: 3300, unit: "Exabyte" },
        { q: "Wie viele Quadratnanometer groß ist die kleinste bekannte Transistorstruktur?", a: 1, unit: "Quadratnanometer" },
        { q: "Wie viele Bits pro Sekunde erreicht der schnellste optische Datentransfer im Labor?", a: 1000000000000000, unit: "Bits pro Sekunde" },
        { q: "Wie viele Qubits hat der leistungsfähigste Quantencomputer?", a: 127, unit: "Qubits" },
        { q: "Wie viele Teraflops Rechenleistung hat der schnellste Supercomputer der Welt?", a: 442000, unit: "Teraflops" },
        { q: "Wie viele Gigabyte Daten kann eine moderne DNA-Speichertechnik pro Kubikmillimeter speichern?", a: 215000, unit: "Gigabyte" },
        { q: "Wie viele Millisekunden Latenz hat eine Satelliteninternetverbindung durchschnittlich?", a: 600, unit: "Millisekunden" },
        { q: "Wie viele Gigahertz Taktrate erreicht der schnellste übertaktete PC-Prozessor?", a: 8.7, unit: "Gigahertz" },
        { q: "Wie viele Bits pro Sekunde überträgt ein einzelnes Neuron im menschlichen Gehirn?", a: 1, unit: "Bit pro Sekunde" },
        { q: "Wie viele Jahre soll eine auf Saphirplatten gespeicherte Daten-Zeitkapsel überdauern?", a: 1000000, unit: "Jahre" },
        { q: "Wie viele Kilobyte Arbeitsspeicher hatte der erste IBM PC?", a: 16, unit: "Kilobyte" },
    ],
        };

        let currentQuizId = "";
        let currentQuestions = [];
        let currentQuestionIndex = 0;
        let answerShown = false;
        let isHost = false;
        let gameId = "";
        let playerScore = 0;
        let opponentScore = 0;

        function selectHost(host) {
            isHost = host;
            document.getElementById("hostSelection").style.display = "none";
            if (isHost) {
                document.getElementById("quizIdInput").style.display = "inline-block";
                document.getElementById("changeIdBtn").style.display = "inline-block";
            } else {
                document.getElementById("gameIdInput").style.display = "block";
            }
        }

        function joinGame() {
            gameId = document.getElementById("gameIdInputField").value;
            // Here you would typically make an API call to join the game
            // For now, we'll just start the quiz with a hardcoded quiz ID
            startQuiz("ET01");
        }

        function startQuiz(quizId) {
            if (quizSets[quizId]) {
                currentQuizId = quizId;
                currentQuestions = [...questions[quizId]];
                if (isHost) {
                    gameId = generateGameId();
                    document.getElementById("gameIdDisplay").innerText = `Game ID: ${gameId}`;
                    document.getElementById("gameIdDisplay").style.display = "block";
                }
                shuffleQuestions(gameId);
                currentQuestionIndex = 0;
                answerShown = false;
                showQuestion();
                updateRemainingQuestions();
                updateThemeIcon();
                document.getElementById("changeIdBtn").innerText = "ID ändern";
                document.getElementById("animation").style.display = "none";
            } else {
                alert("Ungültige Quiz-ID. Bitte versuchen Sie es erneut.");
            }
        }

        function generateGameId() {
            return Math.random().toString(36).substring(2, 8).toUpperCase();
        }

        function shuffleQuestions(seed) {
            let rng = seededRandom(seed);
            for (let i = currentQuestions.length - 1; i > 0; i--) {
                const j = Math.floor(rng() * (i + 1));
                [currentQuestions[i], currentQuestions[j]] = [currentQuestions[j], currentQuestions[i]];
            }
        }

        function seededRandom(seed) {
            let m = 2**35 - 31;
            let a = 185852;
            let s = seed % m;
            return function() {
                return (s = s * a % m) / m;
            };
        }

        function showQuestion() {
            const questionEl = document.getElementById("question");
            const answerEl = document.getElementById("answer");
            const showAnswerBtn = document.getElementById("showAnswerBtn");
            const nextBtn = document.getElementById("nextBtn");
            const prevBtn = document.getElementById("prevBtn");
            const answerSection = document.getElementById("answerSection");
            const unitSelect = document.getElementById("unitSelect");

            questionEl.innerText = currentQuestions[currentQuestionIndex].q;
            answerEl.style.display = "none";
            showAnswerBtn.innerText = "Antwort anzeigen";
            nextBtn.style.display = "none";
            answerShown = false;

            prevBtn.style.display = currentQuestionIndex > 0 ? "inline-block" : "none";
            answerSection.style.display = "block";

            // Populate unit select
            unitSelect.innerHTML = "";
            ["Meter", "Zentimeter", "Kilogramm", "Liter", "Watt", "Volt", "Ampere", "Prozent", "Stück", "Bits", "Gigabyte", "Mbit/s", "Zeichen"].forEach(unit => {
                let option = document.createElement("option");
                option.value = unit;
                option.text = unit;
                unitSelect.appendChild(option);
            });

            // Set the correct unit if specified in the question
            if (currentQuestions[currentQuestionIndex].unit) {
                unitSelect.value = currentQuestions[currentQuestionIndex].unit;
                unitSelect.disabled = true;
            } else {
                unitSelect.disabled = false;
            }

            updateRemainingQuestions();
        }

        function showAnswer() {
            const answerEl = document.getElementById("answer");
            const showAnswerBtn = document.getElementById("showAnswerBtn");
            const nextBtn = document.getElementByI

let currentRole = "";
let currentQuizId = "";
let currentGameId = "";
let currentQuestions = [];
let currentQuestionIndex = 0;
let playerScore = 0;
let opponentScore = 0;
let playerAnswer = null;
let opponentAnswer = null;

function selectRole(role) {
    currentRole = role;
    document.getElementById("roleSelection").style.display = "none";
    if (role === "host") {
        document.getElementById("hostSetup").style.display = "block";
    } else {
        document.getElementById("playerSetup").style.display = "block";
    }
}

function startGame() {
    currentQuizId = document.getElementById("quizIdInput").value.toUpperCase();
    if (quizSets[currentQuizId]) {
        currentGameId = generateGameId();
        currentQuestions = [...questions[currentQuizId]];
        shuffleQuestions(currentGameId);
        initializeGame();
    } else {
        alert("Ungültige Quiz-ID. Bitte versuchen Sie es erneut.");
    }
}

function joinGame() {
    currentGameId = document.getElementById("gameIdInput").value.toUpperCase();
    // In einer echten Anwendung würden Sie hier die Quiz-ID vom Server abrufen
    // Für dieses Beispiel nehmen wir an, dass das Spiel existiert und verwenden eine Standard-Quiz-ID
    currentQuizId = "ET01"; // Dies sollte in einer echten Anwendung dynamisch sein
    currentQuestions = [...questions[currentQuizId]];
    shuffleQuestions(currentGameId);
    initializeGame();
}

function initializeGame() {
    document.getElementById("hostSetup").style.display = "none";
    document.getElementById("playerSetup").style.display = "none";
    document.getElementById("gameInfo").style.display = "block";
    document.getElementById("currentGameId").textContent = currentGameId;
    document.getElementById("currentQuizId").textContent = currentQuizId;
    document.getElementById("animation").style.display = "none";
    showQuestion();
    updateRemainingQuestions();
    updateThemeIcon();
}

function generateGameId() {
    return Math.random().toString(36).substring(2, 8).toUpperCase();
}

function shuffleQuestions(seed) {
    let rng = seededRandom(seed);
    for (let i = currentQuestions.length - 1; i > 0; i--) {
        const j = Math.floor(rng() * (i + 1));
        [currentQuestions[i], currentQuestions[j]] = [currentQuestions[j], currentQuestions[i]];
    }
}

function seededRandom(seed) {
    let m = 2**35 - 31;
    let a = 185852;
    let s = seed % m;
    return function() {
        return (s = s * a % m) / m;
    };
}

function showQuestion() {
    const questionEl = document.getElementById("question");
    const answerEl = document.getElementById("answer");
    const nextBtn = document.getElementById("nextBtn");
    const prevBtn = document.getElementById("prevBtn");
    const answerSection = document.getElementById("answerSection");
    const unitSelect = document.getElementById("unitSelect");
    const submitBtn = document.getElementById("submitAnswerBtn");
    const checkAnswerBtn = document.getElementById("checkAnswerBtn");

    questionEl.innerText = currentQuestions[currentQuestionIndex].q;
    answerEl.style.display = "none";
    nextBtn.style.display = "none";
    answerSection.style.display = "block";
    submitBtn.style.display = "inline-block";
    checkAnswerBtn.style.display = "none";

    prevBtn.style.display = currentQuestionIndex > 0 ? "inline-block" : "none";

    // Populate unit select
    unitSelect.innerHTML = "";
    ["Meter", "Zentimeter", "Kilogramm", "Liter", "Watt", "Volt", "Ampere", "Prozent", "Stück", "Bits", "Gigabyte", "Mbit/s", "Zeichen"].forEach(unit => {
        let option = document.createElement("option");
        option.value = unit;
        option.text = unit;
        unitSelect.appendChild(option);
    });

    // Set the correct unit if specified in the question
    if (currentQuestions[currentQuestionIndex].unit) {
        unitSelect.value = currentQuestions[currentQuestionIndex].unit;
        unitSelect.disabled = true;
    } else {
        unitSelect.disabled = false;
    }

    updateRemainingQuestions();
}

function submitAnswer() {
    const userAnswer = parseFloat(document.getElementById("answerInput").value);
    const unit = document.getElementById("unitSelect").value;
    
    if (isNaN(userAnswer)) {
        alert("Bitte geben Sie eine gültige Zahl ein.");
        return;
    }

    playerAnswer = {value: userAnswer, unit: unit};
    
    document.getElementById("submitAnswerBtn").style.display = "none";
    
    if (currentRole === "host") {
        document.getElementById("checkAnswerBtn").style.display = "inline-block";
    } else {
        // In einer echten Anwendung würden Sie hier die Antwort an den Server senden
        alert("Ihre Antwort wurde übermittelt. Warten Sie auf den Host, um die Ergebnisse zu überprüfen.");
    }
}

function checkAnswer() {
    const correctAnswer = currentQuestions[currentQuestionIndex].a;
    const answerEl = document.getElementById("answer");
    const nextBtn = document.getElementById("nextBtn");

    // In einer echten Anwendung würden Sie hier die Antwort des Gegners vom Server abrufen
    opponentAnswer = {value: Math.random() * correctAnswer * 2, unit: currentQuestions[currentQuestionIndex].unit};

    const playerDiff = Math.abs(playerAnswer.value - correctAnswer);
    const opponentDiff = Math.abs(opponentAnswer.value - correctAnswer);

    let resultText = `Richtige Antwort: ${correctAnswer} ${currentQuestions[currentQuestionIndex].unit}\n`;
    resultText += `Ihre Antwort: ${playerAnswer.value} ${playerAnswer.unit}\n`;
    resultText += `Gegner Antwort: ${opponentAnswer.value} ${opponentAnswer.unit}\n\n`;

    if (playerDiff < opponentDiff) {
        resultText += "Sie waren näher dran! Sie erhalten den Punkt.";
        playerScore++;
    } else if (opponentDiff < playerDiff) {
        resultText += "Ihr Gegner war näher dran. Der Gegner erhält den Punkt.";
        opponentScore++;
    } else {
        resultText += "Unentschieden! Beide erhalten einen Punkt.";
        playerScore++;
        opponentScore++;
    }

    answerEl.innerText = resultText;
    answerEl.style.display = "block";
    nextBtn.style.display = "inline-block";
    document.getElementById("checkAnswerBtn").style.display = "none";

    updateScores();
}

function updateScores() {
    document.getElementById("playerScore").innerText = playerScore;
    document.getElementById("opponentScore").innerText = opponentScore;
}

function nextQuestion() {
    currentQuestionIndex++;
    if (currentQuestionIndex < currentQuestions.length) {
        showQuestion();
    } else {
        endGame();
    }
}

function previousQuestion() {
    if (currentQuestionIndex > 0) {
        currentQuestionIndex--;
        showQuestion();
    }
}

function endGame() {
    const quizContainer = document.querySelector(".quiz-container");
    quizContainer.innerHTML = `
        <h2>Spiel beendet!</h2>
        <p>Ihr Punktestand: ${playerScore}</p>
        <p>Gegner Punktestand: ${opponentScore}</p>
        <button onclick="location.reload()">Neues Spiel starten</button>
    `;
}

function updateRemainingQuestions() {
    const remainingEl = document.getElementById("remainingQuestions");
    remainingEl.innerText = `Frage ${currentQuestionIndex + 1} von ${currentQuestions.length}`;
}

function updateThemeIcon() {
    const iconEl = document.getElementById("themeIcon");
    iconEl.innerText = quizSets[currentQuizId].icon;
}

// Fügen Sie diese Zeilen am Ende des <script>-Tags hinzu:
document.getElementById("submitAnswerBtn").addEventListener("click", submitAnswer);
if (document.getElementById("checkAnswerBtn")) {
    document.getElementById("checkAnswerBtn").addEventListener("click", checkAnswer);
}

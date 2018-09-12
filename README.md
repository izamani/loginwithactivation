#
#  Secure PHP Login

Was kann diese Secure PHP Login System Klasse?
Diese Klasse bietet eine einfache Möglichkeit, neue Benutzer zu registrieren. Es unterstützt einen sicheren PHP-Login-Prozess nach der Aktivierung mit dem Login-Verifizierungscode, der an seine E-Mail-Adresse gesendet wird. Es verwendet jQuery zum Senden von Formularen mit AJAX-Anforderungen an Skripts, die wie eine REST-API funktionieren, aber auch direkt mit PHP-Skripts, die die Anmeldeseiten bedienen.

Diese Klasse bietet auch eine Lösung in PHP, um Benutzer nach einer Reihe von Anmeldeversuchen zu blockieren, dh Sie können eine Reihe von falschen Versuchen angeben, bevor Sie das Konto sperren. Außerdem können Benutzer sich abmelden, ihr Kennwort ändern und abhängig von der Benutzerrolle unterschiedliche Berechtigungen haben.

Da viele von uns PHP 7 oder zumindest PHP 5.5 verwenden, handelt es sich offensichtlich um ein OOP-Anmeldesystem. Da unsere Anwendungen in der Regel die Anmeldung für mehrere Benutzer unterstützen, dh mehrere Benutzer gleichzeitig zugreifen, ist es sehr sinnvoll, Benutzerdatensätze in einer Datenbank zu speichern.

Es unterstützt PDO, so dass Sie Benutzerdatenbankdatensätze mit MySQL, PostgreSQL, SQLite und anderen speichern können. Das hier enthaltene Beispiel erklärt, wie es mit MySQL verwendet wird. Es könnte MySQLi anstelle von PDO verwenden, aber es wird nützlicher sein, wenn Sie diesen Code mit anderen Arten von Datenbanken wiederverwenden können.

Da wir PDO auch mit SQLite verwenden können, können wir zur Implementierung einer PHP-Anmeldung ohne Datenbank einen echten Datenbankserver verwenden, da SQLite die Datenbank in lokalen Dateien speichert.

Diese Klasse könnte auch erweitert werden, um ein Social-Login-System mit OAuth zu implementieren, um Login mit Facebook, Yahoo, Google, YouTube, Gmail, Microsoft, LinkedIn, GitHub, BitBucket, Instagram, Tumblr, Deviantart, WordPress, ODesk und viele andere durchzuführen.

Dafür müssen Sie jedoch auch andere Klassen verwenden, um sich mit OAuth oder sogar LDAP für die Anmeldung anzumelden. Eine gute Idee für zukünftige Verbesserungen ist auch, SMS-Login zu unterstützen, ich benutze eine Methode, um einen Code per SMS zu senden, um den Benutzer beweisen zu lassen, dass er ein bestimmtes Konto besitzt, oder sogar die Mac-Adresse zu überprüfen, um den Zugriff auf Benutzer zu beschränken lokales Netzwerk. Aber jetzt wollen wir es einfach halten.
#

# Das Script PHP Secure Login-Seite starten

Wir benötigen eine Startseite, auf der Besucher sich registrieren oder einloggen können. Ich verwende Bootstrap, um eine einfache Seite zu erstellen, auf der wir zwei Registerkarten haben, eine für die Anmeldung und die andere für die Registrierung.

Wenn sich ein Besucher registriert, müssen wir normalerweise überprüfen, ob er die von ihm eingegebene E-Mail besitzt. Er wird also auch einen Platz brauchen, um den Bestätigungscode zu senden, um seinen neuen Account zu aktivieren. Ich setze das auch in den Login-Tab.

<? php
    require_once '../class/user.php';
    require_once 'config.php';

    $ user-> indexHead ();
    $ user-> indexTop ();
    $ user-> loginForm ();
    $ user-> activationForm ();
    $ user-> indexMiddle ();
    $ user-> registerForm ();
    $ user-> indexFooter ();
?>
Diese Klasse verwendet Vorlagenskripte, die Sie möglicherweise im Verzeichnis inc finden. Wenn Sie sich die Vorlagendateien ansehen, können Sie sehen, dass wir jQuery und Bootstrap verwenden und zwei benutzerdefinierte Dateien verwenden, eine für CSS und die andere für JS.

Im Body habe ich drei von der Klasse automatisch generierte Formularbereiche hinzugefügt: zwei im Login-Bereich und einen im Registrierungsbereich. Sie können auch sehen, dass ich sie in Tabs getrennt habe.

Sie können viele Tutorials über Bootstrap finden, wie Sie mit Tabs arbeiten können. Das erste Formular wird für die Anmeldung, das nächste für die Kontoüberprüfung und das letzte für die Registrierung verwendet.

#
# JavaScript und AJAX-Formularübermittlung

In der Datei main.js verwende ich jQuery- und AJAX-Anfragen. Dies ist wichtig, wenn Sie die REST-API-Methode verwenden möchten. Dies ist sehr nützlich, wenn wir das Frontend vom Backend trennen wollen.

$ (Funktion () {

    $ ('# login-form-link'). click (Funktion (e) {
        $ ("# login-form"). delay (100) .fadeIn (100);
        $ ("# register-form"). fadeOut (100);
        $ ('# register-form-link'). removeClass ('active');
        $ (this) .addClass ('active');
        e.preventDefault ();
    });

    $ ('# register-form-link'). click (Funktion (e) {
        $ ("# register-form"). delay (100) .fadeIn (100);
        $ ("# login-form"). fadeOut (100);
        $ ('# login-form-link'). removeClass ('active');
        $ (this) .addClass ('active');
        e.preventDefault ();
    });
});

Funktion validiereEmail ($ email) {
    var emailReg = /^([\w-\.]+@([\w-]+\.)+[\w-]{2,})$$;
    return emailReg.test ($ email);
}
Die ersten beiden Abschnitte im JavaScript-Code dienen zum Wechseln zwischen Tabs. Als nächstes benutze ich die Submit-Buttons in jeder Form. Da wir jQuery und AJAX verwenden, senden wir das Formular nicht auf die klassische Weise, sondern rufen die Werte aus den Eingaben mit jQuery ab und senden die Werte mithilfe einer AJAX POST-Anfrage an die Backend-PHP-Skripte.

Die letzte Funktion am unteren Rand ist eine Funktion, um zu überprüfen, ob die eingegebene E-Mail-Adresse gültig ist. Es wird nicht überprüft, ob der Server existiert, sondern nur, ob das Format gültig ist.

#
# Das Login-Prozessskript

Zur Anmeldung erhält das Skript die geposteten Daten und übergibt sie an die Login-Methode der Klasse. Wenn die Anmeldung erfolgreich ist, weist sie die Benutzerdaten Sitzungs-Login-Variablen zu und gibt als Ergebnis nichts zurück. Wenn es einen Fehler gibt, wird es gedruckt.

Vergessen Sie nicht, dass wir dieses Skript über AJAX aufrufen, damit alles, was vom Skript ausgegeben wird, als Ergebnis des AJAX-Aufrufs in JavaScript zugestellt wird.

<? php
     require_once '../class/user.php';
     require_once 'config.php';

     $ email = filter_input (INPUT_POST, 'Benutzername', FILTER_SANITIZE_EMAIL);
     $ password = filter_input (INPUT_POST, 'Passwort', FILTER_DEFAULT);

     if ($ user-> login ($ email, $ passwort)) {
         die;
     } else {
         $ user-> printMsg ();
         die;
     }
?>
#
# Das Registrierungsprozess-Skript

Wie beim Login-Prozess ist dies auch ein sehr einfaches Codebeispiel, bei dem ich die Registrierungsmethode in der Benutzerklasse verwende. Wenn eine Registrierung erfolgreich ist, drucken wir stattdessen eine Bestätigungsmeldung oder eine Fehlermeldung.
<?php
    require_once '../class/user.php';
    require_once 'config.php';

    $email = filter_input(INPUT_POST, 'email', FILTER_SANITIZE_EMAIL);
    $fname = filter_input(INPUT_POST, 'fname', FILTER_SANITIZE_STRING);
    $lname = filter_input(INPUT_POST, 'lname', FILTER_SANITIZE_STRING);
    $pass = filter_input(INPUT_POST, 'password', FILTER_DEFAULT);

    if($user->registration($email, $fname, $lname, $pass)) {
        print 'A confirmation mail has been sent, please confirm your account registration!';
        die;
    } else {
        $user->printMsg();
        die;
    }
?>
#
# Das Aktivierungsprozess-Skript
Nach der Registrierung muss der Benutzer seinen Account aktivieren. Daher verwenden wir hier die Methode emailActivation. Wieder ein Forward-Forward-Codebeispiel, das eine Fehlermeldung ausgibt, wenn die Aktivierung fehlschlägt, oder nichts, wenn es passiert.

<?php
    require_once '../class/user.php';
    require_once 'config.php';

    $email = filter_input(INPUT_POST, 'email', FILTER_SANITIZE_EMAIL);
    $code = filter_input(INPUT_POST, 'code', FILTER_DEFAULT);
    
    if($user->emailActivation( $email, $code)) {
        die;
    } else {
        $user->printMsg();
        die;
    }
    ?>
#
# Das Logout-Prozessskript
Für den Logout-Prozess verwende ich die Logout-Methode in der Benutzerklasse. Nachdem der Logout abgeschlossen ist, leiten wir den Benutzer einfach in die index.php um. Der Abmeldevorgang wird in diesem Beispiel nicht über AJAX gestartet. Wenn wir es über AJAX aufrufen, erfolgt die Weiterleitung über JavaScript-Code, also nicht in PHP.
<?php
    require_once '../user.php';
    require_once 'config.php';

    $user->logout();

    header('location: index.php');
?>
#
# Die Konfigurationsskriptdatei
Eine wichtige Datei, die ich beschreiben muss, ist die config.php. Es ist notwendig, die Datenbankverbindungsdetails anzugeben und die Sitzung zu initiieren. Diese Anmelde- und Registrierungsklasse funktioniert nicht ohne Sitzungen. Es wird auch das Benutzerklassenobjekt erstellt, so dass wir es in jeder anderen Datei verwenden können. Wie Sie bereits gesehen haben, haben wir dieses Skript jedes Mal nach dem Kurs selbst eingefügt.

Außerdem müssen wir die Dateien angeben, die wir in den HTML-Generierungsfunktionen verwenden. In diesem Fall user.php, login.php, activate.php und register.php. Sie können diese Dateien anpassen.
<?php
    session_start();
    define('conString', 'mysql:host=localhost;dbname=login');
    define('dbUser', 'root');
    define('dbPass', 'root');

    define('userfile', 'user.php');
    define('loginfile', 'login.php');
    define('activatefile', 'activate.php');
    define('registerfile', 'register.php');

    ini_set('display_errors', 1);
    ini_set('display_startup_errors', 1);
    error_reporting(E_ALL);

    $user = new User();
    $user->dbConnect(conString, dbUser, dbPass);
?>
#
# Das Skript für den protokollierten Benutzerabschnitt

Nach dem Anmelden haben die Benutzer einen Bereich auf der Website, wo sie sehen können, was nicht angemeldete Besucher sehen können, da es nur für registrierte Benutzer ist.

Für dieses Tutorial habe ich ein Beispiel dafür erstellt, was dieser Abschnitt in der Datei user.php sein könnte. Sie können zwei Fälle bemerken. Wenn ein normaler Benutzer angemeldet ist, wird ihm nur seine Information angezeigt. Wenn der Benutzer jedoch ein Administrator ist, ist in diesem Fall seine Rollen-ID 2 (Sie können dies zu einer beliebigen ID-Nummer ändern). Dann sieht er eine Liste aller Benutzer im System.
<?php
   require_once '../class/user.php';
   require_once 'config.php';

   if(IsSet($_SESSION['user']['id']) {
       $user->userPage();
   } else {
      header('Location: index.php');
   }
?>


   


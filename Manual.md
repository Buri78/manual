# Installation


## Python und Editor
Für dieses Projekt benötigen wir die neuste <a href="https://www.python.org/downloads/">Python Version</a> sowie einen 
geeigneten Editor wie <a href="https://www.jetbrains.com/de-de/pycharm/download/#section=windows">PyCharm Community</a>.

In den ```Installation Options``` von PyCharm solltest du bei ```64-bit launcher```, ```Add launchers dir to the PATH```,
```Add "Open Folder as Project"``` und ```.py``` ein Häckchen setzten.

## PIP
Zur Installation und Verwaltung unserer Python Packages verwenden wir PIP. Wurde Python vom obigen Link heruntergeladen, 
sollte PIP bereits installiert sein. Um die Version zu überprüfen, öffne die Windows Eingabeaufforderung: 
```Windows + X```, ```Ausführen```, gib ```cmd.exe``` ein und drücke ```OK```. Nun gibst du:

*Terminal*
```
py -m pip --version
```
ein und die Eingabeaufforderung wird dir deine aktuelle PIP version 
ausgeben. Mit dem folgenden Befehl kannst du PIP aktualisieren:

*Terminal*
```
pip install --upgrade pip
```


## Bloomberg API

### Installiere Bloomberg Professional
<ol>
<li><a href="https://www.bloomberg.com/professional/support/software-updates/">Bloomberg Download</a></li>
<li>Wähle <code>Bloomberg Terminal — New/Upgrade Installation</code></li>
<li>Gehe durch den Installationsassistenten und installiere Bloomberg unbedingt in den folgenden Pfad <code>C:\</code>. 
Der Subordner <code>blp</code> wird automatisch erstellt.</li>
<li>Wähle, verbinde Bloomberg mit dem Internet.</li>
</ol>

### Installiere Visual Studio Build Tools 2017
<ol>
<li><a href="https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=15">Visual Studio Build Tools</a></li>
<li>Führe den Installationsmanager aus und folge den Schritten, bis du zur Auswahl der <code>Workloads</code> gelangst.</li>
<li>Wähle <code>Visual C++ build tools</code> und folgende <code>Installation details</code> aus:</li>

* <code>Windows 10 SDK (10.0.17763.0)</code>
* <code>Visual C++-Tools für CMake</code>
* <code>Kernfunktionen von Testtools – Buildtools</code>
* <code>Visual C++-ATL für x86 und x64</code>
* <code>Visual C++-MFC für x86 und x64</code>
* <code>C++-/CLI-Unterstützung</code>
* <code>VC++ 2015.3, Version 14.00 (v140) – Toolset für ...</code>

<li>Installiere die ausgewählten Workloads. Dies benötigt ca. 10GB Speicher und dauert also eine Weile.</li>
<li>Starte den PC neu</li>
</ol>

### Lade BloombergWindowsSDK herunter
<ol>
<li>Logge dich im Terminal ein</li>
<li>Gebe <code>WAPI</code> in einem Terminal-Fenster ein</li>
<li>Du gelangst ins <code>API Download Center</code> unter den Express Links</li>
<li>Klicke beim Produkt <code>B-Pipe, Server, API, Desktop API and Platform SDK</code> auf Download. Es kann eine Weile
dauern bis der Download startet.</li>
<li>Extrahiere den <code>BloombergWindowsSDK</code> Ordner in den Pfad <code>C:\blp</code></li>
</ol>

### Überschreibe die .dll Files
<ol>
<li>Schliesse das Bloomberg Terminal</li>
<li>Gehe zu <code>C:\blp\BloombergWindowsSDK\C++API\v3.16.1.1\bin\DAPI</code></li>
<li>Kopiere <code>blpapi3_32.dll</code> und <code>blpapi3_64.dll</code></li>
<li>Gehe zu <code>C:\blp\DAPI</code></li>
<li>Füge die kopierten .dll Files ein und überschreibe die Vorhandenen</li>
</ol>




# Setup

Nun öffnen wir PyCharm und starten ein neues Projekt und geben ihm einen beliebigen Namen. Ich nenne es 
```pythonProject```. PyCharm kreiert uns automatisch ein neues Virtual Environment (```venv```) in welches wir nun 
unsere Packages installieren können. 


## Installation Django

Wir öffnen das ```Terminal``` in PyCharm und sehen durch den Zusatz ```(venv)```, dass unser Virtual 
Environment aktiviert ist. Nun installieren wir Django mit dem Befehl:

*Terminal*
```
pip install Django
```

Für später installieren wir jetzt bereits den Bloomberg API:

*Terminal*
```
pip install --index-url=https://bcms.bloomberg.com/pip/simple/ blpapi
```


## Starte ein Django Projekt

Nun starten wir ein Django Projekt. Dazu geben wir im ```Terminal``` diesen Befehl ein:

*Terminal*
```
django-admin startproject _bloombergAPI
```

Dies kreiert uns alle Files und Ordner für unser Django Projekt. Ich setze jeweils vor den Projektnamen 
einen Underscore, damit der Ordner alphabetisch geordnet zuoberst erscheint. Django erstellt uns im Projektordner 
nämliche einen weiteren Ordner mit demselben Namen. Darin befinden sich die grundlegenden Skripts für unser Projekt.


## Kreiere eine App

In dem erstellten Django Projekt können wir nun diverse Applikationen erstellen. Zuerst begeben wir uns im 
```Terminal``` aber in das Projekt ```_bloombergAPI```:

*Terminal*
```
cd _bloombergAPI
```

Nun können wir mit folgendem Befehl eine App names ```api``` erstellen:

*Terminal*
```
python manage.py startapp api
```


## Templates und Static Ordner

Nun erstellen wir im Ordner ```pythonProject/_bloombergAPI``` einen neuen Ordner namens ```_templates```
und einen Ordner names ```_static```. Die Underscores sind wieder gewählt, damit die Ordner zuoberst erscheinen, 
da diese für das ganze Projekt verwendet werden.




# Erstellen unserer View

In einem ersten Schritt laden wir unsere neu erstellte App ```api``` in unser Projekt. Dafür öffnen wir das File 
```_bloombergAPI/settings.py``` und fügen unsere App zu den ```INSTALLED_APPS``` hinzu:

*_bloombergAPI/settings.py*
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'api.apps.ApiConfig',
]
```

Da wir uns gerade im ```settings.py``` File befinden können wir noch zwei weitere Anpassungen vornehmen, welche wir 
später brauchen werden. Wir inkludieren unsere Ordner ```_templates``` und ```_static``` damit Django weiss wo sich 
unsere Templates und Staticfiles befinden:

*_bloombergAPI/settings.py*
```python
import os
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, '_templates')],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]

STATIC_URL = '/_static/'
STATICFILES_DIRS = (os.path.join(BASE_DIR, '_static'),)
```

Im Ordner ```_templates``` erstellen wir einen neuen Ordner names ```api```. Darin werden sich all unsere HTML Files
für diese Applikation befinden. Im Ordner ```_templates/api``` erstellen wir ein neues File namens
```bloomberg_api.html```. Darin fügen wir folgenden HTML code ein:

*_templates/api/bloomberg_api.html*
```HTML
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Bloomberg API</title>
</head>
<body>
    <h1>Hier kommt unser Bloomberg API</h1>
</body>
</html>
```

Nun öffnen wir das File ```api/views.py``` und geben diese HTML Seite wider, indem wir eine View erstellen:

*api/views.py*
```python
from django.shortcuts import render

def BloombergAPI(request):
    return render(request, 'api/bloomberg_api.html', {})
```

Nun begeben wir uns ins File ```_bloombergAPI/urls.py```. Dort fügen wir folgenden Code ein:

*_bloombergAPI/urls.py*
```Python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls, name='admin'),
    path('api/', include('api.urls'))
]
```

Im Ordner ```api``` erstellen wir ein neues File namens ```urls.py``` und füllen es mit folgendem Code:

*api/urls.py*
```Python
from django.urls import path
from .views import BloombergAPI

urlpatterns = [
    path('bloomberg_api.html', BloombergAPI, name='bloomberg_api'),
    ]
```

Wir haben bereits unsere erste Page erstellt. Wir können diese anschauen indem wir im ```Terminal``` unseren Server
starten:

*Terminal*
```
python manage.py runserver
```

Django verwendet standardmässig den development server http://127.0.0.1:8000/. Wir werden hier den Fehler 
```Page not Found``` sehen, da wir unserer Seite den URL ```api/bloomberg_api.html``` gegeben haben. Öffnen wir den URL 
http://127.0.0.1:8000/api/bloomberg_api.html sehen wir die erstellte HTML Seite.

Lasst uns noch eine Homepage erstellen. Dazu kreieren wir eine neue HTML Seite im Ordner ```api/bloomberg_api.html``` und nennen
sie ```home.html```:

*_templates/home.html*
```HTML
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Bloomberg API</title>
</head>
<body>
    <h1>Hier kommt unsere Homepage</h1>
    <a href="{% url 'bloomberg_api' %}">Gehe zum Bloomberg API</a>
</body>
</html>
```

Nun müssen wir für diese HTML Page eine View erstellen. Da die Homepage aber dieselbe für alle Applikationen ist,
erstellen wir diese nicht wie vorhin im File ```api/views.py``` sondern direkt bei im ```_bloombergAPI/urls.py``` File:

*_bloombergAPI/urls.py*
```Python
from django.contrib import admin
from django.urls import path, include
from django.shortcuts import render


def HomePage(request):
    return render(request, 'home.html', {})


urlpatterns = [
    path('admin/', admin.site.urls, name='admin'),
    path('', HomePage, name='home'),
    path('api/', include('api.urls'))
]
```

Öffnen wir nun http://127.0.0.1:8000/ sehen wir unsere Homepage. Wir haben also eine Webpage erstellt, welche die URL 
http://127.0.0.1:8000/ und http://127.0.0.1:8000/api/bloomberg_api.html kennt. Nice!



# HTML Template und CSS

Aktuell sind unsere beiden Seiten noch ziemlich hässlich. Dafür gibts CSS (Cascading Style Sheets). Da ich selbst
absolut kein CSS Profi bin und dies auch sekundär ist, veranschauliche ich einfach an einem Beispiel wie das Ganze
funktioniert. Dann verwenden wir Vorlagen. Im Ordner ```_static``` erstellen wir einen neuen Ordner ```css``` und darin
ein File names ```style.css```:

*_static/css/style.css*
```CSS
body {
  background-color: black;
}

h1 {
  color: white;
  border-top: 1px dotted;
  border-bottom: 5px solid;
  border-color: white;
}

a {
    color: blue;
}

p.specialclass {
  background: yellow;
}
```

Nun inkludieren wir dies (vorerst nur auf der Homepage). Dazu öffnen wir das File ```_templates/home.html``` und ändern
dies zu:

*_templates/home.html*
```HTML
{% load static %}

<head>
    <meta charset="UTF-8">
    <title>Bloomberg API</title>
    <link href="{% static 'css/style.css' %}" rel="stylesheet">
</head>
<body>
    <h1>Hier kommt unsere Homepage</h1>
    <a href="{% url 'bloomberg_api' %}">Gehe zum Bloomberg API</a>
    <p class="specialclass">Dies ist eine eigene Klasse.</p>
</body>
</html>
```

Wenn wir die <a href="http://127.0.0.1:8000/">Homepage</a> mit ```CTRL + Shift + R``` neu laden, sehen wir, 
sehen wir die Änderungen.

Wir möchten die ganzen Stylings und Komponenten (Menü usw.) einer Webseite nicht selbst schreiben. Somit können wir
Templates aus dem Internet verwenden. Davon gibt es unzählige. Wir verwenden Bootstrap, da es weit verbreitet ist. Gehe 
zur <a href="https://startbootstrap.com/theme/sb-admin-2">Bootstrap</a> Webpage und wähle ```Free Download```. Wenn du 
auf die Seite klickst, kannst du die Webpage auch ausprobieren.

Mit dem Download erhalten wir einen gezippten Ordner. Zuerst löschen wir unseren ```css``` Ordner aus 
```_templates/css```. Dann kopieren wir die Ordner ```css```, ```js``` und ```vendor``` aus 
```startbootstrap-sb-admin-2-gh-pages.zip\startbootstrap-sb-admin-2-gh-pages``` und fügen sie in unser Ordner 
```_static``` ein. Zudem kopieren wir das File 
```startbootstrap-sb-admin-2-gh-pages.zip\startbootstrap-sb-admin-2-gh-pages\index.html``` in unseren Ordner
```_templates``` (selbe Ebene wie ```home.html```). Wir benennen dieses File in ```base.html``` um. Dieses HTML File 
bildet die Grundlage für unsere Webseite. Sie enthält alle Elemente die auf jeder URL zu sehen sein sollen. Wenn wir
also eine Änderung am Menü oder der Kopf-/Fusszeile vornehmen möchten, müssen wir das nur an einer Stelle und nicht für
jede URL separat vornehmen. Das bedeutet wir müssen dieses File nun so bearbeiten, dass es alle grundlegenden Bausteine
enthält und darin jeweils Platz für die einzelnen Pages lässt.

Wir bearbeiten den Head wie folgt:

*_templates/base.html*
```HTML
{% load static %}

<html lang="de">

<head>

    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta name="description" content="">
    <meta name="author" content="">

    <title>Unsere Webpage</title>
    
    <!-- Custom styles for this template-->
    <link href="{% static 'vendor/fontawesome-free/css/all.min.css' %}" rel="stylesheet">
    <link href="{% static 'css/sb-admin-2.min.css' %}" rel="stylesheet">

</head>
```

Dann löschen wir den ganzen Teil innerhalb des ```<!-- Begin Page Content -->``` divs und fügen
```block content``` Tags ein:

*_templates/base.html*
```HTML
...
                <!-- End of Topbar -->

                <!-- Begin Page Content -->
                <div class="container-fluid">
                    
                    {% block content %}
                    
                    {% endblock %}

                </div>
                <!-- /.container-fluid -->

            </div>
            <!-- End of Main Content -->

            <!-- Footer -->
...
```

Zudem löschen wir einige Scripts am Ende des Files und ändern auch hier die Pfade, sodass dieses wie folgt aussieht:

*_templates/base.html*
```HTML
...
    <!-- Logout Modal-->
    <div class="modal fade" id="logoutModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel"
        aria-hidden="true">
        <div class="modal-dialog" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="exampleModalLabel">Ready to Leave?</h5>
                    <button class="close" type="button" data-dismiss="modal" aria-label="Close">
                        <span aria-hidden="true">×</span>
                    </button>
                </div>
                <div class="modal-body">Select "Logout" below if you are ready to end your current session.</div>
                <div class="modal-footer">
                    <button class="btn btn-secondary" type="button" data-dismiss="modal">Cancel</button>
                    <a class="btn btn-primary" href="login.html">Logout</a>
                </div>
            </div>
        </div>
    </div>

    <!-- Bootstrap core JavaScript-->
    <script src="{% static 'vendor/jquery/jquery.min.js' %}"></script>
    <script src="{% static 'vendor/bootstrap/js/bootstrap.bundle.min.js' %}"></script>

    <!-- Core plugin JavaScript-->
    <script src="{% static 'vendor/jquery-easing/jquery.easing.min.js' %}"></script>

    <!-- Custom scripts for all pages-->
    <script src="{% static 'js/sb-admin-2.min.js' %}"></script>

</body>

</html>
```

Perfekt. Nun gehen wir in unser File ```_templates/home.html``` und ändern es wie folgt ab:

*_templates/home.html*
```HTML
{% extends 'base.html' %}
{% load static %}

{% block content %}

<body>
    <h1>Hier kommt unsere Homepage</h1>
    <a href="{% url 'bloomberg_api' %}">Gehe zum Bloomberg API</a>
</body>

{% endblock %}
```

Wir laden also das ```base.html``` File und fügen lediglich dort wo wir die ```block content``` Tags eingefügt haben
zusätzlichen HTML Code ein.

Wir machen das Selbe für ```_templates/api/bloomberg_api.html```:

*_templates/api/bloomberg_api.html*
```HTML
{% extends 'base.html' %}
{% load static %}
{% block content %}

<body>
    <h1>Hier kommt unser Bloomberg API</h1>
</body>

{% endblock %}
```

Nun können wir die <a href="http://127.0.0.1:8000/">Homepage</a> mit ```CTRL + Shift + R``` neu laden und haben eine
schöne Webseite mit einem Menü sowie einer Kopf- und Fusszeile. Innerhalb dieses Gerüsts wird nun unsere Homepage und 
wenn wir <a href="http://127.0.0.1:8000/api/bloomberg_api.html">Bloomberg API</a> aufrufen, die API Seite angezeigt.

Jetzt müssen wir unser Template nur noch so anpassen, dass es die Komponenten enthält, welche wir benötigen. Dafür
kommentieren wir einen Grossteil aus. Wir haben aktuell nur eine weitere Seite. Die ```href``` erstezen wir, damit auf
die richtige Seite weitergeleitet wird. Die ```Sidebar``` sieht also nun wie folgt aus:

*_templates/base.html*
```HTML
 <!-- Sidebar -->
        <ul class="navbar-nav bg-gradient-primary sidebar sidebar-dark accordion" id="accordionSidebar">

            <!-- Sidebar - Brand -->
            <a class="sidebar-brand d-flex align-items-center justify-content-center" href="{% url 'home' %}">
                <div class="sidebar-brand-icon rotate-n-15">
                    <i class="fas fa-laugh-wink"></i>
                </div>
                <div class="sidebar-brand-text mx-3">Rahn+Bodmer</div>
            </a>

            <!-- Divider -->
            <hr class="sidebar-divider my-0">

            <!-- Nav Item - Dashboard -->
            <li class="nav-item active">
                <a class="nav-link" href="{% url 'bloomberg' %}">
                    <i class="fas fa-fw fa-tachometer-alt"></i>
                    <span>Bloomberg API</span></a>
            </li>

            <!-- Divider -->
            <hr class="sidebar-divider">

<!--            &lt;!&ndash; Heading &ndash;&gt;-->
<!--            <div class="sidebar-heading">-->
<!--                Interface-->
<!--            </div>-->

<!--            &lt;!&ndash; Nav Item - Pages Collapse Menu &ndash;&gt;-->
<!--            <li class="nav-item">-->
<!--                <a class="nav-link collapsed" href="#" data-toggle="collapse" data-target="#collapseTwo"-->
<!--                    aria-expanded="true" aria-controls="collapseTwo">-->
<!--                    <i class="fas fa-fw fa-cog"></i>-->
<!--                    <span>Components</span>-->
<!--                </a>-->
<!--                <div id="collapseTwo" class="collapse" aria-labelledby="headingTwo" data-parent="#accordionSidebar">-->
<!--                    <div class="bg-white py-2 collapse-inner rounded">-->
<!--                        <h6 class="collapse-header">Custom Components:</h6>-->
<!--                        <a class="collapse-item" href="buttons.html">Buttons</a>-->
<!--                        <a class="collapse-item" href="cards.html">Cards</a>-->
<!--                    </div>-->
<!--                </div>-->
<!--            </li>-->

<!--            &lt;!&ndash; Nav Item - Utilities Collapse Menu &ndash;&gt;-->
<!--            <li class="nav-item">-->
<!--                <a class="nav-link collapsed" href="#" data-toggle="collapse" data-target="#collapseUtilities"-->
<!--                    aria-expanded="true" aria-controls="collapseUtilities">-->
<!--                    <i class="fas fa-fw fa-wrench"></i>-->
<!--                    <span>Utilities</span>-->
<!--                </a>-->
<!--                <div id="collapseUtilities" class="collapse" aria-labelledby="headingUtilities"-->
<!--                    data-parent="#accordionSidebar">-->
<!--                    <div class="bg-white py-2 collapse-inner rounded">-->
<!--                        <h6 class="collapse-header">Custom Utilities:</h6>-->
<!--                        <a class="collapse-item" href="utilities-color.html">Colors</a>-->
<!--                        <a class="collapse-item" href="utilities-border.html">Borders</a>-->
<!--                        <a class="collapse-item" href="utilities-animation.html">Animations</a>-->
<!--                        <a class="collapse-item" href="utilities-other.html">Other</a>-->
<!--                    </div>-->
<!--                </div>-->
<!--            </li>-->

<!--            &lt;!&ndash; Divider &ndash;&gt;-->
<!--            <hr class="sidebar-divider">-->

<!--            &lt;!&ndash; Heading &ndash;&gt;-->
<!--            <div class="sidebar-heading">-->
<!--                Addons-->
<!--            </div>-->

<!--            &lt;!&ndash; Nav Item - Pages Collapse Menu &ndash;&gt;-->
<!--            <li class="nav-item">-->
<!--                <a class="nav-link collapsed" href="#" data-toggle="collapse" data-target="#collapsePages"-->
<!--                    aria-expanded="true" aria-controls="collapsePages">-->
<!--                    <i class="fas fa-fw fa-folder"></i>-->
<!--                    <span>Pages</span>-->
<!--                </a>-->
<!--                <div id="collapsePages" class="collapse" aria-labelledby="headingPages" data-parent="#accordionSidebar">-->
<!--                    <div class="bg-white py-2 collapse-inner rounded">-->
<!--                        <h6 class="collapse-header">Login Screens:</h6>-->
<!--                        <a class="collapse-item" href="login.html">Login</a>-->
<!--                        <a class="collapse-item" href="register.html">Register</a>-->
<!--                        <a class="collapse-item" href="forgot-password.html">Forgot Password</a>-->
<!--                        <div class="collapse-divider"></div>-->
<!--                        <h6 class="collapse-header">Other Pages:</h6>-->
<!--                        <a class="collapse-item" href="404.html">404 Page</a>-->
<!--                        <a class="collapse-item" href="blank.html">Blank Page</a>-->
<!--                    </div>-->
<!--                </div>-->
<!--            </li>-->

<!--            &lt;!&ndash; Nav Item - Charts &ndash;&gt;-->
<!--            <li class="nav-item">-->
<!--                <a class="nav-link" href="charts.html">-->
<!--                    <i class="fas fa-fw fa-chart-area"></i>-->
<!--                    <span>Charts</span></a>-->
<!--            </li>-->

<!--            &lt;!&ndash; Nav Item - Tables &ndash;&gt;-->
<!--            <li class="nav-item">-->
<!--                <a class="nav-link" href="tables.html">-->
<!--                    <i class="fas fa-fw fa-table"></i>-->
<!--                    <span>Tables</span></a>-->
<!--            </li>-->

<!--            &lt;!&ndash; Divider &ndash;&gt;-->
<!--            <hr class="sidebar-divider d-none d-md-block">-->

            <!-- Sidebar Toggler (Sidebar) -->
            <div class="text-center d-none d-md-inline">
                <button class="rounded-circle border-0" id="sidebarToggle"></button>
            </div>

        </ul>
        <!-- End of Sidebar -->
```

Auch beim ```Topbar``` kommentieren wir einen Teil aus:

*_templates/base.html*
```HTML
<!-- Topbar -->
                <nav class="navbar navbar-expand navbar-light bg-white topbar mb-4 static-top shadow">

                    <!-- Sidebar Toggle (Topbar) -->
                    <button id="sidebarToggleTop" class="btn btn-link d-md-none rounded-circle mr-3">
                        <i class="fa fa-bars"></i>
                    </button>

                    <!-- Topbar Search -->
                    <form
                        class="d-none d-sm-inline-block form-inline mr-auto ml-md-3 my-2 my-md-0 mw-100 navbar-search">
                        <div class="input-group">
                            <input type="text" class="form-control bg-light border-0 small" placeholder="Search for..."
                                aria-label="Search" aria-describedby="basic-addon2">
                            <div class="input-group-append">
                                <button class="btn btn-primary" type="button">
                                    <i class="fas fa-search fa-sm"></i>
                                </button>
                            </div>
                        </div>
                    </form>

                    <!-- Topbar Navbar -->
                    <ul class="navbar-nav ml-auto">

<!--                        &lt;!&ndash; Nav Item - Search Dropdown (Visible Only XS) &ndash;&gt;-->
<!--                        <li class="nav-item dropdown no-arrow d-sm-none">-->
<!--                            <a class="nav-link dropdown-toggle" href="#" id="searchDropdown" role="button"-->
<!--                                data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">-->
<!--                                <i class="fas fa-search fa-fw"></i>-->
<!--                            </a>-->
<!--                            &lt;!&ndash; Dropdown - Messages &ndash;&gt;-->
<!--                            <div class="dropdown-menu dropdown-menu-right p-3 shadow animated&#45;&#45;grow-in"-->
<!--                                aria-labelledby="searchDropdown">-->
<!--                                <form class="form-inline mr-auto w-100 navbar-search">-->
<!--                                    <div class="input-group">-->
<!--                                        <input type="text" class="form-control bg-light border-0 small"-->
<!--                                            placeholder="Search for..." aria-label="Search"-->
<!--                                            aria-describedby="basic-addon2">-->
<!--                                        <div class="input-group-append">-->
<!--                                            <button class="btn btn-primary" type="button">-->
<!--                                                <i class="fas fa-search fa-sm"></i>-->
<!--                                            </button>-->
<!--                                        </div>-->
<!--                                    </div>-->
<!--                                </form>-->
<!--                            </div>-->
<!--                        </li>-->

<!--                        &lt;!&ndash; Nav Item - Alerts &ndash;&gt;-->
<!--                        <li class="nav-item dropdown no-arrow mx-1">-->
<!--                            <a class="nav-link dropdown-toggle" href="#" id="alertsDropdown" role="button"-->
<!--                                data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">-->
<!--                                <i class="fas fa-bell fa-fw"></i>-->
<!--                                &lt;!&ndash; Counter - Alerts &ndash;&gt;-->
<!--                                <span class="badge badge-danger badge-counter">3+</span>-->
<!--                            </a>-->
<!--                            &lt;!&ndash; Dropdown - Alerts &ndash;&gt;-->
<!--                            <div class="dropdown-list dropdown-menu dropdown-menu-right shadow animated&#45;&#45;grow-in"-->
<!--                                aria-labelledby="alertsDropdown">-->
<!--                                <h6 class="dropdown-header">-->
<!--                                    Alerts Center-->
<!--                                </h6>-->
<!--                                <a class="dropdown-item d-flex align-items-center" href="#">-->
<!--                                    <div class="mr-3">-->
<!--                                        <div class="icon-circle bg-primary">-->
<!--                                            <i class="fas fa-file-alt text-white"></i>-->
<!--                                        </div>-->
<!--                                    </div>-->
<!--                                    <div>-->
<!--                                        <div class="small text-gray-500">December 12, 2019</div>-->
<!--                                        <span class="font-weight-bold">A new monthly report is ready to download!</span>-->
<!--                                    </div>-->
<!--                                </a>-->
<!--                                <a class="dropdown-item d-flex align-items-center" href="#">-->
<!--                                    <div class="mr-3">-->
<!--                                        <div class="icon-circle bg-success">-->
<!--                                            <i class="fas fa-donate text-white"></i>-->
<!--                                        </div>-->
<!--                                    </div>-->
<!--                                    <div>-->
<!--                                        <div class="small text-gray-500">December 7, 2019</div>-->
<!--                                        $290.29 has been deposited into your account!-->
<!--                                    </div>-->
<!--                                </a>-->
<!--                                <a class="dropdown-item d-flex align-items-center" href="#">-->
<!--                                    <div class="mr-3">-->
<!--                                        <div class="icon-circle bg-warning">-->
<!--                                            <i class="fas fa-exclamation-triangle text-white"></i>-->
<!--                                        </div>-->
<!--                                    </div>-->
<!--                                    <div>-->
<!--                                        <div class="small text-gray-500">December 2, 2019</div>-->
<!--                                        Spending Alert: We've noticed unusually high spending for your account.-->
<!--                                    </div>-->
<!--                                </a>-->
<!--                                <a class="dropdown-item text-center small text-gray-500" href="#">Show All Alerts</a>-->
<!--                            </div>-->
<!--                        </li>-->

<!--                        &lt;!&ndash; Nav Item - Messages &ndash;&gt;-->
<!--                        <li class="nav-item dropdown no-arrow mx-1">-->
<!--                            <a class="nav-link dropdown-toggle" href="#" id="messagesDropdown" role="button"-->
<!--                                data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">-->
<!--                                <i class="fas fa-envelope fa-fw"></i>-->
<!--                                &lt;!&ndash; Counter - Messages &ndash;&gt;-->
<!--                                <span class="badge badge-danger badge-counter">7</span>-->
<!--                            </a>-->
<!--                            &lt;!&ndash; Dropdown - Messages &ndash;&gt;-->
<!--                            <div class="dropdown-list dropdown-menu dropdown-menu-right shadow animated&#45;&#45;grow-in"-->
<!--                                aria-labelledby="messagesDropdown">-->
<!--                                <h6 class="dropdown-header">-->
<!--                                    Message Center-->
<!--                                </h6>-->
<!--                                <a class="dropdown-item d-flex align-items-center" href="#">-->
<!--                                    <div class="dropdown-list-image mr-3">-->
<!--                                        <img class="rounded-circle" src="img/undraw_profile_1.svg"-->
<!--                                            alt="">-->
<!--                                        <div class="status-indicator bg-success"></div>-->
<!--                                    </div>-->
<!--                                    <div class="font-weight-bold">-->
<!--                                        <div class="text-truncate">Hi there! I am wondering if you can help me with a-->
<!--                                            problem I've been having.</div>-->
<!--                                        <div class="small text-gray-500">Emily Fowler · 58m</div>-->
<!--                                    </div>-->
<!--                                </a>-->
<!--                                <a class="dropdown-item d-flex align-items-center" href="#">-->
<!--                                    <div class="dropdown-list-image mr-3">-->
<!--                                        <img class="rounded-circle" src="img/undraw_profile_2.svg"-->
<!--                                            alt="">-->
<!--                                        <div class="status-indicator"></div>-->
<!--                                    </div>-->
<!--                                    <div>-->
<!--                                        <div class="text-truncate">I have the photos that you ordered last month, how-->
<!--                                            would you like them sent to you?</div>-->
<!--                                        <div class="small text-gray-500">Jae Chun · 1d</div>-->
<!--                                    </div>-->
<!--                                </a>-->
<!--                                <a class="dropdown-item d-flex align-items-center" href="#">-->
<!--                                    <div class="dropdown-list-image mr-3">-->
<!--                                        <img class="rounded-circle" src="img/undraw_profile_3.svg"-->
<!--                                            alt="">-->
<!--                                        <div class="status-indicator bg-warning"></div>-->
<!--                                    </div>-->
<!--                                    <div>-->
<!--                                        <div class="text-truncate">Last month's report looks great, I am very happy with-->
<!--                                            the progress so far, keep up the good work!</div>-->
<!--                                        <div class="small text-gray-500">Morgan Alvarez · 2d</div>-->
<!--                                    </div>-->
<!--                                </a>-->
<!--                                <a class="dropdown-item d-flex align-items-center" href="#">-->
<!--                                    <div class="dropdown-list-image mr-3">-->
<!--                                        <img class="rounded-circle" src="https://source.unsplash.com/Mv9hjnEUHR4/60x60"-->
<!--                                            alt="">-->
<!--                                        <div class="status-indicator bg-success"></div>-->
<!--                                    </div>-->
<!--                                    <div>-->
<!--                                        <div class="text-truncate">Am I a good boy? The reason I ask is because someone-->
<!--                                            told me that people say this to all dogs, even if they aren't good...</div>-->
<!--                                        <div class="small text-gray-500">Chicken the Dog · 2w</div>-->
<!--                                    </div>-->
<!--                                </a>-->
<!--                                <a class="dropdown-item text-center small text-gray-500" href="#">Read More Messages</a>-->
<!--                            </div>-->
<!--                        </li>-->

                        <div class="topbar-divider d-none d-sm-block"></div>

                        <!-- Nav Item - User Information -->
                        <li class="nav-item dropdown no-arrow">
                            <a class="nav-link dropdown-toggle" href="#" id="userDropdown" role="button"
                                data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
                                <span class="mr-2 d-none d-lg-inline text-gray-600 small">Douglas McGee</span>
                                <img class="img-profile rounded-circle"
                                    src="img/undraw_profile.svg">
                            </a>
                            <!-- Dropdown - User Information -->
                            <div class="dropdown-menu dropdown-menu-right shadow animated--grow-in"
                                aria-labelledby="userDropdown">
                                <a class="dropdown-item" href="#">
                                    <i class="fas fa-user fa-sm fa-fw mr-2 text-gray-400"></i>
                                    Profile
                                </a>
                                <a class="dropdown-item" href="#">
                                    <i class="fas fa-cogs fa-sm fa-fw mr-2 text-gray-400"></i>
                                    Settings
                                </a>
                                <a class="dropdown-item" href="#">
                                    <i class="fas fa-list fa-sm fa-fw mr-2 text-gray-400"></i>
                                    Activity Log
                                </a>
                                <div class="dropdown-divider"></div>
                                <a class="dropdown-item" href="#" data-toggle="modal" data-target="#logoutModal">
                                    <i class="fas fa-sign-out-alt fa-sm fa-fw mr-2 text-gray-400"></i>
                                    Logout
                                </a>
                            </div>
                        </li>

                    </ul>

                </nav>
                <!-- End of Topbar -->
```

So jetzt funktionieren die Links und wir haben eine schöne Webseite. Möchten wir ein Styling anpassen, können wir 
entweder das CSS file ```sb-admin-2.min.css``` ändern oder (was ich empfehle) dies im ```<head>``` unseres 
```base.html``` files tun:

*_templates/base.html*
```HTML
{% load static %}

<html lang="de">

<head>

    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta name="description" content="">
    <meta name="author" content="">

    <title>Unsere Webpage</title>
    <!-- Custom styles for this template-->

    <link href="{% static 'vendor/fontawesome-free/css/all.min.css' %}" rel="stylesheet">
    <link href="{% static 'css/sb-admin-2.min.css' %}" rel="stylesheet">

    <style>
        .bg-gradient-primary{
            background-color: red !important;
            background-image: none !important;
            }
    </style>

</head>
```




# Bloomberg API

Zur Abfage von Bloomy Daten verwenden wir ein Package:

*Terminal*
```
pip install xbbg
```

Nun geben wir unserer View Daten mit, die auf der HTML Seite dargestellt werden sollen:

*api/views.py*
```python
from django.shortcuts import render
from xbbg import blp


def BloombergAPI(request):
    bb_data = blp.bdh('AAPL US EQUITY', 'PX_LAST').to_dict()
    context = {'data': bb_data}
    return render(request, 'api/bloomberg_api.html', context)
```

Diese Daten möchten wir nun auf der HTML Seite darstellen:

*_templates/api/bloomberg_api.html*
```HTML
{% extends 'base.html' %}
{% load static %}
{% block content %}

<body>
    <h1>Hier kommt unser Bloomberg API</h1>
    {{ data }}
</body>

{% endblock %}
```

Funktioniert. Muss nur noch schön dargestellt werden.

Wir möchten aber Eingabefelder erstellen, in welchen wir den Ticker und die Variable eingeben können und der API die
entsprechenden Daten liefert. Dazu erstellen wir ein Django Formular. Wir erstellen also eine neue Datei innerhalb
unseres ```api``` Ordners und nennen diese ```forms.py```. Wir befüllen diese wie folgt:

*api/forms.py*
```python
from django import forms
from django.forms import TextInput, CharField, DateField


class DateInput(forms.DateInput):
    input_type = 'date'


class BBForm(forms.Form):
    ticker = CharField(widget=TextInput(attrs={'class': 'form-control'}))
    variable = CharField(widget=TextInput(attrs={'class': 'form-control'}))
    start_date = DateField(widget=DateInput(attrs={'class': 'form-control'}))
    end_date = DateField(widget=DateInput(attrs={'class': 'form-control'}))
```

In unserer View stellen wir vorerst mal nur diese Form dar (die Bloomberg Daten kommen später):

*api/views.py*
```python
from django.shortcuts import render
from .forms import BBForm
from xbbg import blp


def BloombergAPI(request):
    form = BBForm()
    context = {'form': form}
    return render(request, 'api/bloomberg_api.html', context)
```

Im HTML File müssen wir nun nicht mehr Daten darstellen, sondern dieses Formular:

*_templates/api/bloomberg_api.html*
```HTML
{% extends 'base.html' %}
{% load static %}
{% block content %}

<body>
    <h1>Hier kommt unser Bloomberg API</h1>
    {{ form.as_p }}
</body>

{% endblock %}
{% extends 'base.html' %}
{% load static %}
{% block content %}

<body>
    <h1>Hier kommt unser Bloomberg API</h1>
    <form method="POST">
        {% csrf_token %}
        {{ form.as_p }}
        <button type="submit" class="btn btn-primary">
            Submit
        </button>
    </form>
</body>

{% endblock %}
```

Sehr schön, dass Formular wird richtig dargestellt. Da wir ja nach Eingabe der Daten ins Formular eine andere Ansicht
(mit den Daten) haben möchten, müssen wir in unserer View einen zwischen ```GET``` und ```POST``` unterscheiden:

*api/views.py*
```python
from django.shortcuts import render
from .forms import BBForm
from xbbg import blp


def BloombergAPI(request):
    if request.method == 'GET':
        form = BBForm()
        context = {'form': form}
        return render(request, 'api/bloomberg_api.html', context)

    elif request.method == "POST":
        form = BBForm(request.POST)
        if form.is_valid():
            ticker = form.cleaned_data['ticker']
            variable = form.cleaned_data['variable']
            start = form.cleaned_data['start_date'].strftime('%Y-%m-%d')
            end = form.cleaned_data['end_date'].strftime('%Y-%m-%d')
            form = BBForm()
            ts = blp.bdh(ticker, variable, start, end)
            data = ts[ticker][variable].to_list()
            labels = ts.index.strftime('%Y-%m-%d').to_list()
            context = {'form': form, 'data': data, 'labels': labels, 'variable': variable, 'ticker': ticker}
        return render(request, 'api/bloomberg_api.html', context)

    else:
        return render(request, 'api/bloomberg_api.html', {})
```

In unserer View stellen wir nun neben der Form auch die Daten dar:

*_templates/api/bloomberg_api.html*
```HTML
{% extends 'base.html' %}
{% load static %}
{% block content %}

<body>
    <h1>Hier kommt unser Bloomberg API</h1>
    <form method="POST">
        {% csrf_token %}
        {{ form.as_p }}
        <button type="submit" class="btn btn-primary">
            Submit
        </button>
    </form>
    <div>
        {{ ticker }}
        {{ variable }}
        {{ labels }}
        {{ data }}
    </div>
</body>

{% endblock %}
```

Das müssen wir nun nur noch als Grafik darstellen und wie sind fertig. Für Webgrafiken ist Javascript mit dem Packet
```Charts.js``` optimal. Zur Integration von ```Charts.js```, müssen wir das Packet in unserem ```base.html``` template 
inkludieren und wir definieren eine Funktion:

*_templates/base.html*
```HTML
...
    <!-- Bootstrap core JavaScript-->
    <script src="{% static 'vendor/jquery/jquery.min.js' %}"></script>
    <script src="{% static 'vendor/bootstrap/js/bootstrap.bundle.min.js' %}"></script>

    <!-- Core plugin JavaScript-->
    <script src="{% static 'vendor/jquery-easing/jquery.easing.min.js' %}"></script>

    <!-- Custom scripts for all pages-->
    <script src="{% static 'js/sb-admin-2.min.js' %}"></script>

    <script src="{% static 'vendor/chart.js/Chart.min.js' %}"></script>

     <script>
        $(document).ready(function(){
          {% block jquery %}{% endblock %}
        })
    </script>

</body>

</html>
```

Unsere ```bloomberg_api.html``` template passen wir wie folgt an:

*_templates/api/bloomberg_api.html*
```HTML
{% extends 'base.html' %}
{% load static %}
{% block content %}

<script>
    {% block jquery %}
        $(document).ready(function() {
            var ctx = document.getElementById('myChart').getContext('2d');
            var myChart = new Chart(ctx, {
                type: 'line',
                data: {
                    labels: {{labels|safe}},
                    datasets: [{
                        label: "{{variable|safe}}",
                        fill: false,
                        data: {{data|safe}},
                        borderColor: [
                            'rgba(255, 99, 132, 1)',
                            'rgba(54, 162, 235, 1)',
                            'rgba(255, 206, 86, 1)',
                            'rgba(75, 192, 192, 1)',
                            'rgba(153, 102, 255, 1)',
                            'rgba(255, 159, 64, 1)'
                        ],
                        borderWidth: 1
                    }]
                },
                options: {
                    tooltips: {
                        enabled: true,
                    },
                    title: {
                        display: true,
                        text: "{{ticker|safe}}"
                    },
                    elements: {
                        point:{
                            radius: 0
                            }
                    },
                    scales: {
                        x: {
                            type: 'timeseries',
                        }
                    }
                }
            });
        });
    {% endblock %}
</script>

<body>
    <h1>Hier kommt unser Bloomberg API</h1>
    <form method="POST">
        {% csrf_token %}
        {{ form.as_p }}
        <button type="submit" class="btn btn-primary">
            Submit
        </button>
    </form>
    <div>
        <canvas id="myChart" width="400" height="200"></canvas>
    </div>
</body>

{% endblock %}
```

We are done!




# Plotly Dash App
Nun machen wir das Selbe mit einem Plotly Dash App. Dies erleichtert uns das Leben, da es uns den Javascript Code
selbst erstellt und wir vordefinierte Widgets benutzen können. Dafür erstellen wir eine neue App (was nicht nötig wäre,
wir aber aus Übungszwecken machen):

*Terminal*
```
python manage.py startapp dash_api
```

Wir inkludieren die App in unser Projekt:

*_bloombergAPI/settings.py*
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'api.apps.ApiConfig',
    'dash_api.apps.DashApiConfig',
]
```

und erstellen einen neuen URL Pfad:

*_bloombergAPI/urls.py*
```Python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls, name='admin'),
    path('', HomePage, name='home'),
    path('api/', include('api.urls')),
    path('dash_api/', include('dash_api.urls'))
]
```

Im Ordner ```_templates``` erstellen wir einen neuen Ornder namens ```dash_api``` und darin ein neues HTML File 
```bloomberg_dash_api.html```:

*_templates/dash_api/bloomberg_dash_api.html*
```HTML
{% extends 'base.html' %}
{% load static %}
{% block content %}

<body>
    <h1>Hier kommt unser Bloomberg Dash API</h1>
</body>

{% endblock %}
```

Dann brauchen wir eine view, die unsere Seite rendert:

*dash_api/views.py*
```python
from django.shortcuts import render

def BloombergDashAPI(request):
    return render(request, 'dash_api/bloomberg_dash_api.html', {})
```

Zuletzt erstellen wir im Ordner ```dash_api``` ein file namens ```urls.py```:

*dash_api/urls.py*
```Python
from django.urls import path
from .views import BloombergDashAPI


urlpatterns = [
    path('bloomberg_api.html', BloombergDashAPI, name='bloomberg-dash'),
    ]

```

Wir starten den Server:

*Terminal*
```
python manage.py runserver
```

und besuchen die Seite http://127.0.0.1:8000/dash_api/bloomberg_dash_api.html. Die Seite ist erstellt. Wir möchten
in unserem Sidebar eine Verlinkung auf diese Seite erstellen. Wir öffnen also unser ``base.html``` File und bearbeiten
dieses wie folgt:

*_templates/base.html*
```HTML
...
<!-- Page Wrapper -->
    <div id="wrapper">

        <!-- Sidebar -->
        <ul class="navbar-nav bg-gradient-primary sidebar sidebar-dark accordion" id="accordionSidebar">

            <!-- Sidebar - Brand -->
            <a class="sidebar-brand d-flex align-items-center justify-content-center" href="{% url 'home' %}">
                <div class="sidebar-brand-icon rotate-n-15">
                    <i class="fas fa-laugh-wink"></i>
                </div>
                <div class="sidebar-brand-text mx-3">Rahn+Bodmer</div>
            </a>

            <!-- Divider -->
            <hr class="sidebar-divider my-0">

            <!-- Nav Item - Dashboard -->
            <li class="nav-item active">
                <a class="nav-link" href="{% url 'bloomberg' %}">
                    <i class="fas fa-fw fa-tachometer-alt"></i>
                    <span>Bloomberg API</span></a>
            </li>

            <!-- Divider -->
            <hr class="sidebar-divider">

            <!-- Nav Item - Dashboard -->
            <li class="nav-item active">
                <a class="nav-link" href="{% url 'bloomberg-dash' %}">
                    <i class="fas fa-fw fa-columns"></i>
                    <span>Bloomberg Dash API</span></a>
            </li>

            <!-- Divider -->
            <hr class="sidebar-divider">

<!--            &lt;!&ndash; Heading &ndash;&gt;-->
<!--            <div class="sidebar-heading">-->
<!--                Interface-->
<!--            </div>-->
...
```

Supi, jetzt können wir das Dash App erstellen. Im Ordner ```dash_api``` erstellen wir einen neuen Ordner 
```dash_apps```. Darin erstellen wir ein File ```bloomberg_dash.py```. Für unser Plotly Dash benötigen wir einige 
pip install django-plotly-dashPakete. Das dauert ziemlich lange:

*Terminal*
```
pip install dash plotly django-plotly-dash
```

Nun müssen wir nochmals zwei Änderungen an unserem ```settings.py``` File vornehmen:

*_bloombergAPI/settings.py*
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'api.apps.ApiConfig',
    'dash_api.apps.DashApiConfig',
    'django_plotly_dash.apps.DjangoPlotlyDashConfig'
]

MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    'django_plotly_dash.middleware.BaseMiddleware',
    'django_plotly_dash.middleware.ExternalRedirectionMiddleware',
]

X_FRAME_OPTIONS = 'SAMEORIGIN'
```

und im ```_bloombergAPI/urls.py``` File, folgende Anpassung vornehmen:

*_bloombergAPI/urls.py*
```python
from django.contrib import admin
from django.urls import path, include
from django.shortcuts import render


def HomePage(request):
    return render(request, 'home.html', {})


urlpatterns = [
    path('admin/', admin.site.urls, name='admin'),
    path('', HomePage, name='home'),
    path('api/', include('api.urls')),
    path('dash_api/', include('dash_api.urls')),
    path('django_plotly_dash/', include('django_plotly_dash.urls'))
]
```

Für unsere Dash App erstellen wir ein eigenes CSS File. Dazu erstellen wir eine Datei ```dash.css``` im Ordner
```_static/css/```

*_statics/css/dash.css*
```CSS
/* Grid
–––––––––––––––––––––––––––––––––––––––––––––––––– */
.container {
  position: relative;
  width: 100%;
  max-width: 960px;
  margin: 0 auto;
  padding: 0 20px;
  box-sizing: border-box; }
.column,
.columns {
  width: 100%;
  float: left;
  box-sizing: border-box; }

/* For devices larger than 400px */
@media (min-width: 400px) {
  .container {
    width: 85%;
    padding: 0; }
}

/* For devices larger than 550px */
@media (min-width: 550px) {
  .container {
    width: 80%; }
  .column,
  .columns {
    margin-left: 4%; }
  .column:first-child,
  .columns:first-child {
    margin-left: 0; }

  .one.column,
  .one.columns                    { width: 4.66666666667%; }
  .two.columns                    { width: 13.3333333333%; }
  .three.columns                  { width: 22%;            }
  .four.columns                   { width: 30.6666666667%; }
  .five.columns                   { width: 39.3333333333%; }
  .six.columns                    { width: 48%;            }
  .seven.columns                  { width: 56.6666666667%; }
  .eight.columns                  { width: 65.3333333333%; }
  .nine.columns                   { width: 74.0%;          }
  .ten.columns                    { width: 82.6666666667%; }
  .eleven.columns                 { width: 91.3333333333%; }
  .twelve.columns                 { width: 100%; margin-left: 0; }

  .one-third.column               { width: 30.6666666667%; }
  .two-thirds.column              { width: 65.3333333333%; }

  .one-half.column                { width: 48%; }

  /* Offsets */
  .offset-by-one.column,
  .offset-by-one.columns          { margin-left: 8.66666666667%; }
  .offset-by-two.column,
  .offset-by-two.columns          { margin-left: 17.3333333333%; }
  .offset-by-three.column,
  .offset-by-three.columns        { margin-left: 26%;            }
  .offset-by-four.column,
  .offset-by-four.columns         { margin-left: 34.6666666667%; }
  .offset-by-five.column,
  .offset-by-five.columns         { margin-left: 43.3333333333%; }
  .offset-by-six.column,
  .offset-by-six.columns          { margin-left: 52%;            }
  .offset-by-seven.column,
  .offset-by-seven.columns        { margin-left: 60.6666666667%; }
  .offset-by-eight.column,
  .offset-by-eight.columns        { margin-left: 69.3333333333%; }
  .offset-by-nine.column,
  .offset-by-nine.columns         { margin-left: 78.0%;          }
  .offset-by-ten.column,
  .offset-by-ten.columns          { margin-left: 86.6666666667%; }
  .offset-by-eleven.column,
  .offset-by-eleven.columns       { margin-left: 95.3333333333%; }

  .offset-by-one-third.column,
  .offset-by-one-third.columns    { margin-left: 34.6666666667%; }
  .offset-by-two-thirds.column,
  .offset-by-two-thirds.columns   { margin-left: 69.3333333333%; }

  .offset-by-one-half.column,
  .offset-by-one-half.columns     { margin-left: 52%; }

}


/* Base Styles
–––––––––––––––––––––––––––––––––––––––––––––––––– */
/* NOTE
html is set to 62.5% so that all the REM measurements throughout Skeleton
are based on 10px sizing. So basically 1.5rem = 15px :) */
html {
  font-size: 62.5%; }
body {
  font-size: 1.5em; /* currently ems cause chrome bug misinterpreting rems on body element */
  line-height: 1.6;
  font-weight: 400;
  font-family: "Open Sans", "HelveticaNeue", "Helvetica Neue", Helvetica, Arial, sans-serif;
  color: rgb(50, 50, 50); }


/* Typography
–––––––––––––––––––––––––––––––––––––––––––––––––– */
h1, h2, h3, h4, h5, h6 {
  margin-top: 0;
  margin-bottom: 0;
  font-weight: 300; }
h1 { font-size: 4.5rem; line-height: 1.2;  letter-spacing: -.1rem; margin-bottom: 2rem; }
h2 { font-size: 3.6rem; line-height: 1.25; letter-spacing: -.1rem; margin-bottom: 1.8rem; margin-top: 1.8rem;}
h3 { font-size: 3.0rem; line-height: 1.3;  letter-spacing: -.1rem; margin-bottom: 1.5rem; margin-top: 1.5rem;}
h4 { font-size: 2.6rem; line-height: 1.35; letter-spacing: -.08rem; margin-bottom: 1.2rem; margin-top: 1.2rem;}
h5 { font-size: 2.2rem; line-height: 1.5;  letter-spacing: -.05rem; margin-bottom: 0.6rem; margin-top: 0.6rem;}
h6 { font-size: 2.0rem; line-height: 1.6;  letter-spacing: 0; margin-bottom: 0.75rem; margin-top: 0.75rem;}

p {
  margin-top: 0; }


/* Blockquotes
–––––––––––––––––––––––––––––––––––––––––––––––––– */
blockquote {
  border-left: 4px #fff solid;
  padding-left: 1rem;
  margin-top: 2rem;
  margin-bottom: 2rem;
  margin-left: 0rem;
}


/* Links
–––––––––––––––––––––––––––––––––––––––––––––––––– */
a {
  color: #1EAEDB;
  text-decoration: underline;
  cursor: pointer;}
a:hover {
  color: #0FA0CE; }


/* Buttons
–––––––––––––––––––––––––––––––––––––––––––––––––– */
.button,
button,
input[type="submit"],
input[type="reset"],
input[type="button"] {
  display: inline-block;
  height: 38px;
  padding: 0 30px;
  color: #555555;
  text-align: center;
  font-size: 11px;
  font-weight: 600;
  line-height: 38px;
  letter-spacing: .1rem;
  text-transform: uppercase;
  text-decoration: none;
  white-space: nowrap;
  background-color: #fff;
  border-radius: 4px;
  border: 1px solid #bbb;
  cursor: pointer;
  box-sizing: border-box; }
.button:hover,
button:hover,
input[type="submit"]:hover,
input[type="reset"]:hover,
input[type="button"]:hover,
.button:focus,
button:focus,
input[type="submit"]:focus,
input[type="reset"]:focus,
input[type="button"]:focus {
  color: #333;
  border-color: #888;
  outline: 0; }
.button.button-primary,
button.button-primary,
input[type="submit"].button-primary,
input[type="reset"].button-primary,
input[type="button"].button-primary {
  color: #FFF;
  background-color: #96C0CE;
  border-color: #96C0CE; }
.button.button-primary:hover,
button.button-primary:hover,
input[type="submit"].button-primary:hover,
input[type="reset"].button-primary:hover,
input[type="button"].button-primary:hover,
.button.button-primary:focus,
button.button-primary:focus,
input[type="submit"].button-primary:focus,
input[type="reset"].button-primary:focus,
input[type="button"].button-primary:focus {
  color: #FFF;
  background-color: #395775;
  border-color: #395775; }


/* Forms
–––––––––––––––––––––––––––––––––––––––––––––––––– */
input[type="email"],
input[type="number"],
input[type="search"],
input[type="text"],
input[type="tel"],
input[type="url"],
input[type="password"],
textarea,
select {
  height: 38px;
  padding: 6px 10px; /* The 6px vertically centers text on FF, ignored by Webkit */
  background-color: #fff;
  border: 1px solid #D1D1D1;
  border-radius: 4px;
  box-shadow: none;
  box-sizing: border-box;
  font-family: inherit;
  font-size: inherit; /*https://stackoverflow.com/questions/6080413/why-doesnt-input-inherit-the-font-from-body*/}
/* Removes awkward default styles on some inputs for iOS */
input[type="email"],
input[type="number"],
input[type="search"],
input[type="text"],
input[type="tel"],
input[type="url"],
input[type="password"],
textarea {
  -webkit-appearance: none;
     -moz-appearance: none;
          appearance: none; }
textarea {
  min-height: 65px;
  padding-top: 6px;
  padding-bottom: 6px; }
input[type="email"]:focus,
input[type="number"]:focus,
input[type="search"]:focus,
input[type="text"]:focus,
input[type="tel"]:focus,
input[type="url"]:focus,
input[type="password"]:focus,
textarea:focus,
select:focus {
  border: 1px solid #395775;
  outline: 0; }
label,
legend {
  display: block;
  margin-bottom: 0px; }
fieldset {
  padding: 0;
  border-width: 0; }
input[type="checkbox"],
input[type="radio"] {
  display: inline; }
label > .label-body {
  display: inline-block;
  margin-left: .5rem;
  font-weight: normal; }


/* Lists
–––––––––––––––––––––––––––––––––––––––––––––––––– */
ul {
  list-style: circle inside; }
ol {
  list-style: decimal inside; }
ol, ul {
  padding-left: 0;
  margin-top: 0; }
ul ul,
ul ol,
ol ol,
ol ul {
  margin: 1.5rem 0 1.5rem 3rem;
  font-size: 90%; }
li {
  margin-bottom: 1rem; }


/* Tables
–––––––––––––––––––––––––––––––––––––––––––––––––– */
table {
  border-collapse: collapse;
}
th:not(.CalendarDay),
td:not(.CalendarDay) {
  padding: 12px 15px;
  text-align: left;}
th:first-child:not(.CalendarDay),
td:first-child:not(.CalendarDay) {
  padding-left: 0; }
th:last-child:not(.CalendarDay),
td:last-child:not(.CalendarDay) {
  padding-right: 0; }


/* Spacing
–––––––––––––––––––––––––––––––––––––––––––––––––– */
button,
.button {
  margin-bottom: 0rem; }
input,
textarea,
select,
fieldset {
  margin-bottom: 0rem; }
pre,
dl,
figure,
table,
form {
  margin-bottom: 0rem; }
p,
ul,
ol {
  margin-bottom: 0.75rem; }

/* Utilities
–––––––––––––––––––––––––––––––––––––––––––––––––– */
.u-full-width {
  width: 100%;
  box-sizing: border-box; }
.u-max-full-width {
  max-width: 100%;
  box-sizing: border-box; }
.u-pull-right {
  float: right; }
.u-pull-left {
  float: left; }


/* Misc
–––––––––––––––––––––––––––––––––––––––––––––––––– */
hr {
  margin-top: 3rem;
  margin-bottom: 3.5rem;
  border-width: 0;
  border-top: 1px solid #E1E1E1; }


/* Clearing
–––––––––––––––––––––––––––––––––––––––––––––––––– */

/* Self Clearing Goodness */
.container:after,
.row:after,
.u-cf {
  content: "";
  display: table;
  clear: both; }


/* For a nicer datepicker look */
.DateRangePickerInput{
  border: 0px;
  background-color: transparent;
}

/* Media Queries
–––––––––––––––––––––––––––––––––––––––––––––––––– */
/*
Note: The best way to structure the use of media queries is to create the queries
near the relevant code. For example, if you wanted to change the styles for buttons
on small devices, paste the mobile query code up in the buttons section and style it
there.
*/


/* Larger than mobile */
@media (min-width: 400px) {}

/* Larger than phablet (also point when grid becomes active) */
@media (min-width: 550px) {}

/* Larger than tablet */
@media (min-width: 750px) {}

/* Larger than desktop */
@media (min-width: 1000px) {}

/* Larger than Desktop HD */
@media (min-width: 1200px) {}
```

Nun sind wir bereit die App selbst zu schreiben:

*dash_api/dash_apps/bloomberg_dash.py*
```Python
from django_plotly_dash import DjangoDash
import dash_html_components as html
import dash_core_components as dcc
from dash.dependencies import Output, Input, State


app = DjangoDash('bloomberg_dash')
app.css.append_css({'external_url': '/_static/css/dash.css'})

# Write the HTML Code for it
app.layout = html.Div(
    [
        html.Div([
            html.Div([dcc.Input()], className='four columns'),
            html.Div([dcc.Input()], className='four columns'),
            html.Div([dcc.DatePickerRange()], className='four columns'),
        ])
    ]
)
```

Wir laden diese App im ```urls.py``` File:

*dash_api/urls.py*
```Python
from django.urls import path
from .views import BloombergDashAPI
from .dash_apps import bloomberg_dash


urlpatterns = [
    path('bloomberg_dash_api.html', BloombergDashAPI, name='bloomberg-dash'),
    ]
```

und passen das HTML Template wie folgt an:

*_templates/dash_api/bloomberg_dash_api.html*
```HTML
{% extends 'base.html' %}
{% load static %}
{% block content %}

    {% load plotly_dash %}
    {% plotly_app_bootstrap name='bloomberg_dash' %}

{% endblock %}
```

Nun führen wir zwei Befehle im ```Terminal``` aus:

*Terminal*
```
python manage.py makemigations
python manage.py migrate
```
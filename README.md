# Installation


## Python und Editor
Für dieses Projekt benötigen wir die neuste <a href="https://www.python.org/downloads/">Python Version</a> sowie einen 
geeigneten Editor wie <a href="https://www.jetbrains.com/de-de/pycharm/download/#section=windows">PyCharm</a>.


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

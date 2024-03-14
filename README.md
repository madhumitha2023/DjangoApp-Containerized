🚀 Step-By-Step guide in building and containerizing a simple Django application on AWS EC2! 🌐

Step 1️⃣: Setting Up Django on EC2:

I have written a basic single web page django application and hosting it on my AWS EC2.

the flow of this application is, reffering django documentation install django using pip command and the pre-requisite is Python. Commands to follow, "sudo apt update" (EC2), "sudo apt install python3" , "sudo apt install python3-pip", "sudo pip3 install django", "django-admin --version"(to verify). Start a new project using a command "django-admin startproject devops", navigate to the directory where you want to create the project and run. "django-admin", the above command is used to create a skeleton of my project which makes a life easier for coders. Now my "devops" project is created but inside it is nothing related to our application but it is overall my project configuration. When you navigate inside the project which acts as a container for our project "devops" root directory, the available files are:

"devops" -> the  actual Python package for our project and if we need to import anything inside our project, we need to specify this package name with a module(ex: devops.urls)

"manage.py" -> Django's command-line utility for administrative tasks.

Navigate to the "devops" sub folder and you find the following files:

"devops/__init__.py" -> An empty file that tells Python that this directory should be considered a Python package.

"devops/settings.py" ->  Has the entire information of,  the IPs that we can whitelist, info on the database that we connect, secret keys and any secure information, Allowed Hosts, supports django middlewear, templates and also web server gateway interface etc. This settings file sets the entire configuration of our overall project.

"devops/asgi.py": An entry-point for ASGI-compatible web servers to serve our project.

"devops/wsgi.py": An entry-point for WSGI-compatible web servers to serve our project.

"devops/urls.py" -> Responsible for serving the content . If the users use the context route "/demo"(in my case) with the instance ip address and the application port, (ex: in the browser, http://35.183.94.221:8000/demo), the urls understand that if anybody tries to use /demo or /admin, it will serve the content in the demo application.

Till now we have set up our project base and Now Let's see what is this demo application and how to create it. 

Step 2️⃣: Creating a Django App:

To create an application type the following command: "python manage.py startapp <demo(name of the application>". Again this will create a bunch of files inside a "demo" directory.

demo/

    __init__.py

    admin.py

    apps.py

    migrations/

        __init__.py

    models.py

    tests.py

    views.py

Inside "views.py", write the actual python code, Here what I have written a very simple python, index function is defined which renders an HTML file. I have placed this customized HTML file inside the "templates" folder(Manually create this folder inside the demo app) and named it as "demo_site.html". From here the content get served.

To call the view, we need to map it to a URL - and for this we need a URLconf.

To create a URLconf in the "demo" directory, create a file called urls.py. Inside this file write a code as shown in the screenshots to call the view file and its function index.

The next step is to point the root URLconf at the demo.urls module. In devops/urls.py, add an import for django.urls.include and insert an include() in the urlpatterns list, Refer the screenshots for the code. The include() function allows referencing other URLconfs. Whenever Django encounters include(), it chops off whatever part of the URL matched up to that point and sends the remaining string to the included URLconf for further processing.

This is the overall workflow of django application.

Step 3️⃣: Containerizing with Docker:

Now as a Devops engineer our job is to containerize(package and bundle everything for our application to run)  this django application. 

Install Docker on the machine -> "sudo apt install docker.io -y"

Write a "Dockerfile". Please refer a screenshot for the complete code. In this file i have used a base image ubuntu using the command "FROM", and chose a working directory as "/app" (it can be any standard directory that everyone follows in an organization) using the command "WORKDIR". Next I am packaging both source code and requirements.txt in "/app", I am copying all the required python dependencies in requirements.txt and devops directory to my working directory "/app" using the "COPY" command.

Since I have used my base image as ubuntu, I am installing the python and pip using the "RUN" command. This step could be avoided if we are using the base image as python.

Using the same "RUN" command, to update "apt-get update", "pip install -r requirements.txt to download the dependencies. In the start or execute commands "ENTRYPOINT" and "CMD", I have used "python3" for ENTRYPOINT so my executable should not be overridden and cannot be changed, used "manage.py", "runserver", "port to be exposed" in "CMD" command due to their configurable nature.

Next, Build the Dockerfile using the command "docker build -t dockerhubusername/buildname:latest"

type "docker images" and verify that image built is available. Then, run the following command to run your container in an interactive mode: "docker run -p 8000:8000 -it dockerhubusername/buildname:latest". Once the container is successfully running, Type the following command inside your favorite browser "http://instance/hostip:8000/demo".

Hurray! Django application that we had containerized is running on browser.

Happy Learning!

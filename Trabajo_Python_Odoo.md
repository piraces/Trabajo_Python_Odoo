

    import erppeek
    from __future__ import print_function
    SERVER = 'http://localhost:8069'
    DATABASE = 'desarrollo'
    USERNAME = 'companyfirebird@gmail.com'
    PASSWORD = 'platano-1'

La documentación necesaria para poder superar este ejercicio se encuentra en la documentación de [ERPpeek](http://erppeek.readthedocs.org/en/latest/tutorial.html)



### Tarea 1 - Conexión

Demuestra que sabes conectarte a una instancia de Odoo y listar todas sus bases de datos. 


    client = erppeek.Client(server=SERVER)
    
    for database in client.db.list():
        print('Base de datos: %r' % (database,))

    Base de datos: 'desarrollo'
    Base de datos: 'sandbox'


### Tarea 2 - Volcado de datos

Demuestra que puedes imprimir el `id` y el nombre de todos los usuarios (son registros del modelo `res.users`).


    client = erppeek.Client(SERVER, DATABASE, USERNAME, PASSWORD)
    
    proxy = client.model('res.users')
    users = proxy.browse([])
    
    for user in users:
        a = "{user.id} {user.name}".format(user=user)
        print(a)


    1 Administrator
    6 Raul Piraces Alastuey
    7 Luis Jesus Pellicer
    8 Howard Harrison
    10 Michelle Hernandez
    11 Steve Carter
    13 Andrea Hudson


###Tarea 3 - Crear y configurar una base de datos
Demuestra que sabes crear una base de datos, listar todos los módulos instalados por defecto, y si no está presente un módulo determinado instalarlo.


    DATABASE = 'trabajoPython'
    ADMIN_PASSWORD = 'admin'
    
    client = erppeek.Client(server=SERVER)
    
    if not DATABASE in client.db.list():
        print("La BD no existe y se va a crear...")
        client.create_database(ADMIN_PASSWORD, DATABASE)
        print("Base de datos creada")
        # Procedemos a listar todos los modulos instalados por defecto
        installed_modules = client.modules(installed=True)
        print("Lista de modulos instalados:")
        for module in installed_modules['installed']:
            print(module)
        # Se comprueba si esta presente el modulo CRM
        print("Comprobando modulo CRM...")
        modules = client.modules('crm', installed=False)
        if 'crm' in modules['uninstalled']:
            # Si no esta instalado se instala
            client.install('crm')
            print("Modulo CRM instalado.")
        else:
            # El modulo ya esta instalado
            print("El modulo CRM ya estaba instalado...")
            

    La BD no existe y se va a crear...
    Base de datos creada
    Lista de modulos instalados:
    im_chat
    auth_crypt
    base
    base_import
    bus
    im_odoo_support
    report
    web
    web_calendar
    web_diagram
    web_gantt
    web_graph
    web_kanban
    web_kanban_gauge
    web_tests
    web_view_editor
    Comprobando modulo CRM...
    1 module(s) selected
    14 module(s) to process:
      to install	crm
      to install	mail
      to install	auth_signup
      to install	base_action_rule
      to install	base_setup
      to install	board
      to install	calendar
      to install	email_template
      to install	fetchmail
      to install	portal
      to install	resource
      to install	sales_team
      to install	share
      to install	web_kanban_sparkline
    Modulo CRM instalado.


### Tarea 4 - Explorar un modelo

Demuesta que sabes listar todos los campos  del modelo `res.users`, incluyendo nombre, tipo y etiqueta


    DATABASE = 'desarrollo'
    client = erppeek.Client(SERVER, DATABASE, USERNAME, PASSWORD)
    
    proxy = client.model('res.users')
    users = proxy.browse([])
    
    for user in users:
        print("Usuario: {user.name}, Tipo: {user.type}, Etiqueta (alias): {user.alias_id}".format(user=user))

    Usuario: Administrator, Tipo: contact, Etiqueta (alias): companyfirebird@gmail.com
    Usuario: Raul Piraces Alastuey, Tipo: contact, Etiqueta (alias): Inactive Alias
    Usuario: Luis Jesus Pellicer, Tipo: contact, Etiqueta (alias): Inactive Alias
    Usuario: Howard Harrison, Tipo: contact, Etiqueta (alias): Inactive Alias
    Usuario: Michelle Hernandez, Tipo: contact, Etiqueta (alias): Inactive Alias
    Usuario: Steve Carter, Tipo: contact, Etiqueta (alias): Inactive Alias
    Usuario: Andrea Hudson, Tipo: contact, Etiqueta (alias): Inactive Alias


###Tarea 5 - Poblar un modelo

Crea el código neccesario para migrar los usuarios de una base de datos a otra base de datos. No es necesario migrar todos los campos. Basta con una prueba de concepto. 


    DATABASE1 = 'desarrollo'
    DATABASE2 = 'sandbox'
    USERNAME1 = 'companyfirebird@gmail.com'
    PASSWORD1 = 'platano-1'
    USERNAME2 = 'admin'
    PASSWORD2 = 'admin'
    
    origen = erppeek.Client(SERVER, DATABASE1, USERNAME1, PASSWORD1)
    destino = erppeek.Client(SERVER, DATABASE2, USERNAME2, PASSWORD2)
    
    proxy1 = origen.model('res.users')
    proxy2 = destino.model('res.users')
    users = proxy1.browse([])
    
    print("Migrando usuarios entre origen y destino...")
    for user in users:
        login = user.login
        name = user.name
        password = user.password
        proxy2.create({'login': login, 'name': name, 'password' : password})
        print("Usuario: " + name + ". Creado correctamente")
    print("Se han migrado los usuarios correctamente.")

    Migrando usuarios entre origen y destino...
    Usuario: Administrator. Creado correctamente
    Usuario: Raul Piraces Alastuey. Creado correctamente
    Usuario: Luis Jesus Pellicer. Creado correctamente
    Usuario: Howard Harrison. Creado correctamente
    Usuario: Michelle Hernandez. Creado correctamente
    Usuario: Steve Carter. Creado correctamente
    Usuario: Andrea Hudson. Creado correctamente
    Se han migrado los usuarios correctamente.

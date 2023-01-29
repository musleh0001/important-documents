<div align="center">
    <h1>Odoo</h1>
</div>

# App Structure

```
app_name/
├── controllers/
│   ├── __init__.py
│   └── ...
├── data/
│   └── ...
├── demo/
│   └── ...
├── models/
│   ├── __init__.py
│   └── ...
├── reports/
│   └── ...
├── security/
│   └── ...
├── static/
│   ├── description
│   |   └── ...
│   └── src
│       ├── css
│       ├── img
│       ├── js
│       ├── less
│       ├── lib
│       ├── scss
│       └── xml
├── views/
│   └── ...
├── wizards/
│   └── ...
├── __init__.py
└── __manifest__.py
```

## Create custom module

```bash
sudo /odoo/odoo-server/odoo-bin scaffold <module_name> 
    /odoo/custom/addons
# permission
sudo chmod -R 777 /odoo/custom/addons/<module_name>
```



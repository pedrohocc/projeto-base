# API REST com Django e Django REST Framework (DRF)

## 📌 O que você vai construir
Uma API de tarefas com rotas para listar, criar, atualizar (PUT/PATCH) e excluir recursos, usando **Django + DRF**.

---

## ✅ Pré-requisitos
- Python 3.10+ instalado  
- `pip` e `venv` (ambiente virtual)  
- VS Code/Git (opcional)

---

## 1) Preparar o ambiente
Crie e ative um ambiente virtual, e instale Django e DRF:
```bash
python -m venv .venv
# Linux/macOS
source .venv/bin/activate
# Windows (PowerShell)
.venv\Scripts\Activate.ps1

pip install django djangorestframework
```

---

## 2) Criar o projeto e o aplicativo
```bash
django-admin startproject myproject .
python manage.py startapp api
```

---

## 3) Registrar apps no `settings.py`
```python
INSTALLED_APPS = [
    # ...
    'rest_framework',
    'api',
]
```

---

## 4) Model: definindo a entidade
```python
# api/models.py
from django.db import models

class Tarefa(models.Model):
    titulo = models.CharField(max_length=100)
    descricao = models.TextField(blank=True)
    concluida = models.BooleanField(default=False)

    def __str__(self):
        return self.titulo
```

---

## 5) Migrar o banco de dados
```bash
python manage.py makemigrations
python manage.py migrate
```

---

## 6) Serializer: conversão e validação
```python
# api/serializers.py
from rest_framework import serializers
from .models import Tarefa

class TarefaSerializer(serializers.ModelSerializer):
    class Meta:
        model = Tarefa
        fields = '__all__'
```

---

## 7) ViewSet: operações CRUD
```python
# api/views.py
from rest_framework import viewsets
from .models import Tarefa
from .serializers import TarefaSerializer

class TarefaViewSet(viewsets.ModelViewSet):
    queryset = Tarefa.objects.all()
    serializer_class = TarefaSerializer
```

---

## 8) Rotas: registrando URLs com Router
```python
# myproject/urls.py
from django.contrib import admin
from django.urls import path, include
from rest_framework import routers
from api.views import TarefaViewSet

router = routers.DefaultRouter()
router.register(r'tarefas', TarefaViewSet)

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include(router.urls)),
]
```

---

## 9) Executar o servidor e testar
```bash
python manage.py runserver
```

### Endpoints disponíveis
- **GET** `/tarefas/` — listar tarefas  
- **POST** `/tarefas/` — criar tarefa  
- **GET** `/tarefas/<id>/` — detalhe  
- **PUT** `/tarefas/<id>/` — atualizar tudo  
- **PATCH** `/tarefas/<id>/` — atualizar parcialmente  
- **DELETE** `/tarefas/<id>/` — excluir  

---

### Testes rápidos com curl
```bash
# Listar
curl -X GET http://127.0.0.1:8000/tarefas/

# Criar
curl -X POST http://127.0.0.1:8000/tarefas/   -H "Content-Type: application/json"   -d '{"titulo": "Estudar DRF", "concluida": false}'

# Atualizar tudo (PUT)
curl -X PUT http://127.0.0.1:8000/tarefas/1/   -H "Content-Type: application/json"   -d '{"titulo": "Estudar DRF", "descricao": "cap. 1", "concluida": true}'

# Atualizar parcialmente (PATCH)
curl -X PATCH http://127.0.0.1:8000/tarefas/1/   -H "Content-Type: application/json"   -d '{"concluida": true}'

# Excluir
curl -X DELETE http://127.0.0.1:8000/tarefas/1/
```

---

## 10) Browsable API e testes na Web
O DRF oferece uma interface web para explorar e testar endpoints.  
Acesse a raiz da API no navegador e execute requisições sem Postman/Insomnia.

---

## 🌟 Extras úteis (opcional)

### Paginação e filtros (`settings.py`)
```python
REST_FRAMEWORK = {
    "DEFAULT_PAGINATION_CLASS": "rest_framework.pagination.PageNumberPagination",
    "PAGE_SIZE": 10,
    "DEFAULT_FILTER_BACKENDS": [
        "rest_framework.filters.SearchFilter",
        "rest_framework.filters.OrderingFilter",
    ],
    "DEFAULT_PERMISSION_CLASSES": [
        "rest_framework.permissions.IsAuthenticatedOrReadOnly",
    ],
    "DEFAULT_AUTHENTICATION_CLASSES": [
        "rest_framework.authentication.SessionAuthentication",
        "rest_framework.authentication.TokenAuthentication",
    ],
}
```

---

### Habilitar CORS
```bash
pip install django-cors-headers
```
```python
# settings.py
INSTALLED_APPS += ["corsheaders"]
MIDDLEWARE = ["corsheaders.middleware.CorsMiddleware", *MIDDLEWARE]
CORS_ALLOWED_ORIGINS = [
    "http://localhost:3000",
    "http://127.0.0.1:3000",
]
```

---

### Estrutura final do projeto
```
.
├─ api/
│  ├─ migrations/
│  ├─ models.py
│  ├─ serializers.py
│  ├─ views.py
├─ myproject/
│  ├─ settings.py
│  ├─ urls.py
├─ manage.py
└─ requirements.txt
```

---

## 💡 Dicas finais
- Use ViewSets e Routers para reduzir código repetitivo.  
- Prefira `ModelSerializer` sempre que possível.  
- Use `search_fields` e `ordering_fields` para melhorar buscas.  
- Documente endpoints (ex.: `drf-spectacular` ou `drf-yasg`).  
- Para produção: use gunicorn + nginx e variáveis de ambiente.

<div align="center">
       <h1>Django</h1>
</div>

## Table of Contents

- [Create virtualenv and activate](#create-virtualenv-and-activate)

- [Create Django project](#create-django-project)

- [Create django app](#create-django-app)

- [Run django on local server](#run-django-on-local-server)

- [Run migration](#run-migration)

- [Create super user](#create-super-user)

- [Create record in database](#create-record-in-database)

- [Get record from database](#get-record-from-database)

- [Model Register on Admin site](#model-register-on-admin-site)

- [Complex Search](#complex-search)

#### Create virtualenv and activate

```shell
virtualenv venv -p python3
source venv/bin/activate

or

pipenv shell
```

#### Create django project

```shell
django-admin startproject project_name
```

#### Create django app

```shell
python manage.py startapp app_name
```

#### Run django on local server

```shell
python manage.py runserver localhost:8000
```

#### Run migration

```shell
python manage.py makemigrations
python manage.py migrate
```

#### Create super user

```shell
python manage.py createsuperuser
```

#### Create record in database

```python
obj = Article(title="test", content="test content")
obj.save()

or

obj = Article.objects.create(title="test", content="test content")
```

#### Get record from database

```python
obj = Article.objects.get(id=1)
```

#### Model Register on Admin site

```python
class ModelNameAdmin(admin.ModelAdmin):
    pass
admin.site.register(model_name, ModelNameAdmin)

or

@admin.register(model_name)
class ModelNameAdmin(admin.ModelAdmin):
    pass
```

#### Validate single forms field

```python
# def clean_(field_name):
def clean_title(self):
    cleaned_data = self.cleaned_data
    title = cleaned_data.get('title')
    if title.lower().strip() == 'the office':
        raise forms.ValidationError('This title is taken.')
    return title 
```

#### Validate all forms fields

```python
def clean(self):
    cleaned_data = self.cleaned_data
    title = cleaned_data.get('title')
    content = cleaned_data.get('content')

    if title.lower().strip() == 'the office':
        self.add_error('title', 'this title is taken')
    if 'office' in content or 'office' in title.lower():
        self.add_error('content', 'office cannot be in content')

    return cleaned_data
```

#### Slug field

```python
from django.utils.text import slugify

class ModelName(models.Model):
    slug = models.SlugField(blank=True, null=True)

    def save(self, *args, **kwargs):
        if self.slug is None:
            self.slug = slugify(self.title)
        super().save(*args, **kwargs)
```

#### Django Signal

```python
from django.db.models.signals import pre_save, post_save
from django.utils.text import slugify

def slugify_instance_title(instance, save=False):
    qs = Article.objects.filter(slug=slug).exclude(id=instance.id)

    if qs.exists():
        slug = f"{slug}-{qs.count() + 1}"

    slug = slugify(instance.title)
    instance.slug = slug

    if save:
        instance.save()

def article_pre_save(sender, instance, *args, **kwargs):
    if instance.slug is None:
        slugify_instance_title(instance, save=False)
pre_save.connect(article_pre_save, sender=Article)

def article_post_save(sender, instance, created, *args, **kwargs):
    if created:
        slugify_instance_title(instance, save=True)
post_save.connect(article_post_save, sender=Article)
```

#### Django QuerySets & Lookups

```python
qs = Article.objects.all()
print(qs.count()) # print total query

# show only title whose have "Hello World" exact and case insensative
qs = Article.objects.filter(title__iexact="Hello World")

# show all title contain "Hello" and case insensative
qs = Article.objects.filter(title__icontains="Hello")
```

#### Slug in dynamic urls

```python
urlpatterns = [
    path('articles/<slug:slug>/', view.details, name="detail"),
]

def details(request, slug=None):
    Article.objects.get(slug=slug)
```

#### Get absolute url

```python
from django.urls import reverse

def get_absolute_url(self):
    return reverse("detail", kwargs={ "slug": self.slug })
    # return f"/articles/{self.slug}/"
```

#### Complex Search

```python
from django.db.models import Q

lookups = Q(title__icontains=query) | Q(content__icontains=query)
```

#### Model Managers

```python
from django.db.models import Q

class ArticleQuerySet(models.QuerySet):
    def search(self, query=None):
        if query is None or query == "":
            return self.none() # []
        lookups = Q(title__icontains=query) | Q(content__icontains=query)
        # return Article.objects.filter(lookups)
        return self.filter(lookups)

class ArticleManager(models.Manager):
    def get_queryset(self):
        return ArticleQuerySet(self.model, using=self._db)

    def search(self, query=None):
        # if query is None or query == "":
            # return self.get_queryset().none() # []
        # lookups = Q(title__icontains=query) 
        #            | Q(content__icontains=query)
        # return Article.objects.filter(lookups)
        # return self.get_queryset().filter(lookups)
        return self.get_queryset().search(query=query)

class Article(models.Model):
    # ... fields
    objects = ArticleManager()

# Query
Article.objects.filter(title__icontains="something").search(query=query)
```

#### Foreign Key relations

```python
user = models.ForeignKey(
                            User, 
                            related_name="users",
                        on_delete=models.SET_NULL
                        )
```

#### Handle two forms

```python
form = FormName()
form2 = Form2Name()

if all([form.is_valid(), form2.is_valid()]):
    fk_parent = form.save(commit=False)
    fk_parent.save()
    fk_child = form2.save(commit=False)
    fk_child.recipe = fk_parent
    fk_child.save()
```

#### Model Formset Factory

```python
from django.forms.models import modelformset_factory

# Formset = modelformset_factory(Model, form=ModelForm, extra=0)
RecipeFormset = modelformset_factory(Recipe, form=Recipe, extra=0)
qs = obj.recipeingredient_set.all()
formset = RecipeFormset(request.POST or None, queryset=qs)

if formset.is_valid():
    for form in formset:
        form.save()
    # formset.save()
```

<div align="center">
    <h1>Django Rest Framework</h1>
</div>

#### Get random record from database

```python
Product.objects.all().order_by("?").first()
```

#### @api_view

```python
from rest_framework.decorators import api_view
from rest_framework.response import Response

@api_view(["GET"])
def home_view(request, *args, **kwargs):
    serializer = ProductSerializer(request.data)
    if serializer.is_valid(raise_exception=True):
        instance = serializer.save()
        return Response({ "status": "success", "data": data })
    return Response({ "status": "Please insert valid input" })
```

#### Serializer

```python
from rest_framework import serializers

class ProductSerializer(serializers.ModelSerializer):
    # rename serializer field
    my_discount = serializers.SerializerMethodField(read_only=True)

    class Meta:
        model = Product
        fields = ("title", "price", "my_discount")

    def get_my_discount(self, obj):
        if not hasattr(obj, 'id'):
            return None
        if not isinstance(obj, Product):
            return None
        return obj.get_discount()
```

#### Generic API View

```python
from rest_framework import generics

class ProductDetailAPIView(generics.RetrieveAPIView):
    # queryset = Product.objects.all()
    serializer_class = ProductSerializer
    lookup_field = 'pk'

    def get_queryset(self):
        return Product.objects.filter(title__icontians="Mobile")
```

#### Generic Create View

```python
from rest_framework.generics import CreateAPIView

class ProductCreateAPIView(CreateAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer

    # overrider create method
    def perform_create(self, serializer):
        title = serializer.validated_data.get("title")
        title = title.upper()
        serializer.save(title=title)
```

#### Permissions

```python
from rest_framework import permissions

class ProductAPIView(ListAPIView):
    permission_classes = (permissions.IsAuthenticated,)
    authentication_classes = (authentication.SessionAuthentication,)
```

#### Custom permissions

```python
from rest_framework import permissions

class IsStaffEditorPermission(permissions.DjangoModelPermissions):
    def has_permissions(self, request, view):
        user = request.user
        if user.is_staff:
            #app_name.action_modelName
            if user.has_perm("products.add_product"): 
                return True
            if user.has_perm("products.delete_product"):
                return True
            if user.has_perm("products.change_product"): 
                return True
            if user.has_perm("products.view_product"):
                return True
            return False
        return False

class IsStaffEditorPermission(permissions.DjangoModelPermissions):
    def has_permissions(self, request, view):
        if not request.user.is_staff:
            return False
        return super().has_permissions(request, view)
```

#### Viewsets

```python
# viewsets.py
from rest_framework import viewsets

class ProductViewSet(viewsets.ModelViewSet):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    lookup_field = 'pk'

# routers.py
from rest_framework.routers import DetaultRouter
from .viewsets import ProductViewSet

router = DefaultRouter()
router.register('products', ProductViewSet, basename="products")
# products-list, products-detail
# urlpatterns = router.urls

# urls.py
urlpatterns = [
    path("api/v2/", include("app.routers.urls")),
]
```

#### Create link

```python
from rest_framework.reverse import reverse

class ProductSerializer(serializers.ModelSerializer):
    old_url = serializers.SerializerMethodField(read_only=True)
    # only work on modelSerializer
    url = serializers.HyperlinkedIdentityField(
        view_name="product-detail",
        lookup_filed='pk'
    )

    fields = ['old_url', 'url']

    def get_old_url(self, obj):
        request = self.context.get("request")
        if request is None:
            return None
        return reverse("product-detail", 
                        kwargs={"pk": obj.pk}, 
                        request=request)
```

#### Serializer create method

```python
def create(self, validated_data):
    email = validated_data.pop("email")
    # send email code
    # return Product.objects.create(**validated_data)
    return super().create(validated_data)
```

#### Customer validation in serializer

```python
# ... code
class ProductSerializer(serializers.ModelSerializer):
    # ... code

    # another way, take list of validator
    title = serializers.CharField(validators=[validate_title])

    # def validate_<fieldname>
    def validate_title(self, value):
        qs = Products.objects.filter(title__iexact=value)
        if qs.exists():
            raise serializers.ValidationError(f"{value}" exists.)
        return value

# built-in validator
from rest_framework.validators import UniqueValidator

unique_title = UniqueValidator(queryset=Product.objects.all(), 
lookup="iexact")
```

#### Serializing related_field data

```python
# serializers.py
from rest_framework import serializers

class UserPublicSerializer(serializers.Serializer):
    username = serializers.CharField(read_only=True)

class ProductSerializer(serializers.ModelSerializer):
    # user = UserPublicSerializer(read_only=True)
    owner = UserPublicSerializer(source="user", read_only=True)
    field = ["owner"]
```

#### Python to JSON

```python
import json
json.dumps(data) -> This is used to convert python object into json
                    string.
json.loads(data) -> This is used to parse json strings.
```

# Django Query

#### List all row in a table

```python
from django.db import connection
from .models import Student

posts = Student.objects.all()

# print SQL
print(posts.query)

# print SQL with performance
print(connection.queries)
```

#### OR operation

```python
from django.db.models import Q

posts = Student.objects.filter(Q(surname__startswith="musleh")
         | ~Q(surname__startswith="something"))
```

#### AND operation

```python
from django.db.models import Q

posts = Student.objects.filter(Q(surname__startswith="musleh")
         & Q(surname__startswith="khan"))
```

#### Union

```python
posts = Student.objects.all().values_list("firstname")
        .union(Teacher.objects.all().values_list("firstname"))
print(posts)
```

#### Not Query

```python
from django.db.models import Q

# remove student whose age is greater than 19
posts = Student.objects.exclude(age__gt=19)

posts = Student.objects.filter(~Q(age__gt=20) 
        & ~Q(surname__startswith="baldwin"))
```

#### raw SQL query

```python
# Note: Return deferred model instances just like yield
Student.objects.raw("SELECT * FROM student_student WHERE age=20")

# print value
Student.objects.raw("SELECT * FROM student_student WHERE age=20")[:2]
```

#### 

#### Performing custom SQL directly

```python
from django.db import connection

cursor = connection.cursor()
cursor.execute("SELECT * FROM student_student WHERE age > 20")
r = cursor.fetchall()
print(r)
```

#### Model Inheritance

###### Django has 3 type of inheritance options

- Abstract models
  
  - When you have common information needed for number of other models
  
  - ABC - does not get created, fields added to other child classes

- Multi-table model inheritance
  
  - Every model is a model all by itself
  
  - One-to-one link is created automatically

- Proxy models
  
  - Change the behavior of a model
  
  - Proxy models operate on the original model

```python
# Abstract Model
class BaseItem(models.Model):
    title = models.CharField(max_length=255)
    created = models.DateTimeField(auto_now_add=True)
    updated = models.DateTimeField(auto_now=True)

    class Meta:
        abstract = True
        ordering = ['title']

class ItemA(BaseItem):
    content = models.TextField()

    class Meta(BaseItem.Meta): # Meta inheritance
        ordering = ['-created']
```

```python
# Multi-table inheritance
class Books(models.Model):
    title = models.CharField(max_length=255)
    created = models.DateTimeField(auto_now_add=True)

class ISBN(Books):
    books_ptr = models.OneToOneField(
                    Books, 
                    on_delete=models.CASCADE,
                    parent_link=True,
                    primary_key=True
                )
    ISBN = models.TextField()
```

```python
# Proxy Models
class BookContent(models.Model):
    title = models.CharField(max_length=255)
    created = models.DateTimeField(auto_now_add=True)

class BookOrders(BookContent):
    objects = NewManager()

    class Meta:
        proxy = True
        ordering = ['created']

    def created_on(self):
        return timezone.now() - self.created
```

#### django-debug-toolbar - towards optimization

- System information

- Timing

- Setting / Configurations

- Header

- SQL

- Templates, includes

#### Extensible Data Modeling - Django ORM

- Solution 1: Concrete table inheritance

- Solution 2: Multi-table inheritance

- Solution 3: Abstract models

- Solution 4: Polymorphism

```python
from django.contrib.contenttypes.models import ContentType
from django.contrib.contenttypes.fields import GenericForeignKey

class Product(models.Model):
    content_type = models.ForeignKey(
                     ContentType, 
                     on_delete=models.CASCADE,
                     limit_choices_to={'model__in': ('book', 'cupboard')}
                    )
    object_id = models.PositiveIntegerField()
    item = GenericForeignKey('content_type', 'object_id')

    class Meta:
        ordering = ['object_id']

class Book:
    pass
class Cupboard:
    pass
```

#### Transaction Atomicity

###### ACID

- Atomic / Atomicity

- Consistency

- Isolated

- Durable

```python
from django.db import transaction

@transaction.atomic
def payment(request):
    # do something...
    pass

def payment(request):
    entries = Entry.objects.select_for_update()
                .filter(author=request.user) # lock the row
    with transaction.atomic():
        # do something ...
    pass
```

#### Import data from CSV

```shell
cat filename.sql | python manage.py dbshell
```

#### Aggregation

```python
from django.db.models import Sum, Min, Max, Avg

Book.objects.aggregate(total_rating=Sum('ratings_count'))
```

#### PostgreSQLFull Text Search

- allows documents to be preprocessed and an index saved for later rapid searching

- optionally to sort by relevence

- fuzzy search for misspelling

- accent / language support

- weighting, categorization, highlighting

```python
# Case sensitive
book.objects.get(headline__contains="harry")

# Case insensitive
book.objects.get(headline_icontains="harry")

# analyze and execution time
book.objects.filter(title__icontains="harry").explain(analyze=True)
```

```python
INSTALLED_APPS += [
    'django.contrib.postgres',
]

# query
book.objects.filter(title__search="harry")
```

```python
from django.contrib.postgres.search import SearchVector

# SearchVector (search against multiple fields)
results = Book.objects.annotate(search=SearchVector('title', 'authors'),)
                        .filter(search=q)
```

```python
from django.contrib.postgres.search import (
                SearchQuery,
                SearchRank,
                SearchVector,
            )

vector = SearchVector("title")
query = SearchQuery(q)
print(
        Book.objects.annotate(rank=SearchRank(vector, query))
        .order_by("-rank")
        .explain(analyze=True)
     )
results = Book.objects.annotate(rank=SearchRank(vector, query)).order_by(
                "-rank"
            )
```

```python
from django.contrib.postgres.search import (
                SearchQuery,
                SearchRank,
                SearchVector,
            )

vector = SearchVector("title", weight="B") + SearchVector(
                "authors", weight="A"
            )
query = SearchQuery(q)
results = Book.objects.annotate(rank=SearchRank(vector, query)).order_by(
                "-rank"
            )
```

```python
from django.contrib.postgres.search import (
                SearchQuery,
                SearchRank,
                SearchVector,
            )

vector = SearchVector("title", weight="B") + SearchVector(
                "authors", weight="A"
            )
query = SearchQuery(q)
results = Book.objects.annotate(
                rank=SearchRank(vector, query, cover_density=True))
            .order_by("-rank")
```

```python
from django.contrib.postgres.search import TrigramSimilarity

# Note: Have to install pg_trgm extension on postgresql
# similarity__gte=[0.1, 0.3, 0.6, 0.8]
results = (
                Book.objects.annotate(
                    similarity=TrigramSimilarity("title", q),
                )
                .filter(similarity__gte=0.1)
                .order_by("-similarity")
            )
```

```python
from django.contrib.postgres.search import (
                TrigramSimilarity,
                TrigramDistance,
            )
```

#### Postgres indexes

```python
# settings
INSTALLED_APPS = [
    "django.contrib.postgres",
]

# Model
from django.contrib.postgres.indexes import GinIndex

class Book(models.Model):
    title = models.CharField(..., db_index=True)

    class Meta:
        indexes = [
            GinIndexes(name="NewGinIndex", fields=['title'], opclasses=[
                'gin_trgm_ops'
            ])
        ]
# Note: Then makemigrations

# migrations
from django.contrib.postgres.operations import BtreeGinExtension

# ...
operations = [
    BtreeGinExtension(),
]
# Install extension

# views
from django.contrib.postgres.search import TrigramSimilarity

class index(request):
    # ...
    q = request.cleaned_data.get('q')
    results = Book.objects.filter(title__trigram_similar=q)
                .annotate(similar=TrigramSimilarity("title", q))
                .order_by("-similar")
```

#### get vs filter

```python
# Get return DoesNotExists error if data is not present
Student.objects.get(pk=1)

# Filter return Empty queryset if data is not present
Student.objects.filter(pk=1)
```

#### Signal pre_delete, post_delete

```python
from django.db.models.signals import pre_delete, post_delete
from django.dispatch import receiver

from .models import Student

@receiver(pre_delete, sender=Student)
def pre_delete_profile(sender, **kwargs):
    print("You are about to delete something")

@receiver(post_delete, sender=Student)
def post_delete_profile(sender, **kwargs):
    print("You are about to delete something")
```

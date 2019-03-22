# Description

Supported Patterns:
* Repository
* Chain of Responsibility

# Repository

First implements the repository class this code takes on that a entity is the model of django. The class RepositoryBase on contains shortcuts. The philosophy behind is to create a repository.py file to save business logic. Like app this:
```
/product
    __init__.py
    views.py
    models.py
    repository.py <- implement yout logic here
    ...
```

Exemple of repository.py

```
from djpatterns.repository import RepositoryBase
from .models import YourModel

class ProductRepository(RepositoryBase):

    class Meta:
        model = YourModel
```

Fine, you now can access shortcuts of repository. 
For exemple
All method:
```
ProductRepository.all()
#returns all model
```
Get method:
```
ProductRepository.get(pk=1)
#returns model 
```
Filter method:
```
ProductRepository.filter(name__startswith='guitar')
#returns QuerySet
```

Exemple of bussiness logic in the repository class like this in the *repository.py*:
```
class ProductRepository(RepositoryBase):

    class Meta:
        model = Product

    @classmethod
    def buy_product(cls,pk):
        product = cls.get(pk=pk)
        #have in stock
        if product.amount > 0:
            product.amount -= 1
            product.save()
            return 'Sold :)'
        else:
            raise Exception('Out of stock :(')
```
One time logic implemented in you *view.py* only call the method :
```
from product.repository import ProductRepository

# Create your views here.
class SellProduct(generic.View):

    def get(self,request,pk):
        try:
            rs = ProductRepository.buy_product(pk)
            return HttpResponse(rs)
        except Exception as e:
            return HttpResponse(str(e))
```

# Chain of Responsibility

In chain of responsibility you need create one class for each responsability. Your class will inherit of class *RenposibilityBase*. Now you need only override handle method. You can pass data like dict in parameters

```
from djpatterns.responsability import ResponsabilityBase
from djpatterns.exception import ValidationChainException

class ValidateData(ResponsabilityBase):

    def handle(self,data):
        if data:
            return True
        else:
            raise ValidationChainException('Nao recebi uma data')
    
class ValidateNome(ResponsabilityBase):

    def handle(self,data):
        if 'nome' in data:
            return True
        else:
            raise ValidationChainException('Nao tem o campo nome')

class ValidateCpf(ResponsabilityBase):

    def handle(self,data):
        if 'cpf' in data:
            return True
        else:
            raise ValidationChainException('Nao tem o campo cpf')
```

After class created you set next class of chain and call revolver method for first class of your chain

```
c1 = ValidateData()
c2 = ValidateNome()
c3 = ValidateCpf()

c1.set_next(c2)
c2.set_next(c3)

data = {'nome':'Lucas','cpf':'06125'}

try:
    c1.resolver(data)
except ValidationChainException as e:
    print(str(e))
else:
    print('passou')

```

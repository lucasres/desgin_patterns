# Description

Supported Patterns:
* Repository
* Chain of Responsibility

# Repository

First implements the repository class this code takes on that a entity is the model of django
```
from djpatterns.repository import RepositoryBase
from .models import YourModel

class ProductRepository(RepositoryBase):

    class Meta:
        model = YourModel
```

Fine, you now can access shortcuts of repository 
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

You must implements you bussiness logic in the repository class like this in the *repository.py*:
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

in the *view.py*:
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


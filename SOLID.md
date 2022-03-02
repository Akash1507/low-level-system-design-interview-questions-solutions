# SOLID Principles

## Single Responsibility Principle

- A class should have only one responsibility and only one reason to change.
- A class does not perform multiple jobs.
- If you find that you need to change a class or module for multiple reasons, it could likely be a violation of this principle.

```
class Car:
    def __init__(self, car_name):
        self.car_name = car_name
    def get_car_name(self):
        pass

    def add_car_to_garage(self, garage):
        pass
```

```
class Car:
    def __init__(self, car_name):
        self.car_name = car_name
    def get_car_name(self):
        pass

class Garage:
    def __init__(self):
        # garage has a list of cars
        self.cars = []
    def add_car(self, car):
        pass
    def remove_car(self, car):
        pass
```

## Open Close Principle

- Software entities (classes, function, module) open for extension, but not for modification (or closed for modification)
- We shouldn’t need to change existing codes to add new functionalities. One easy way to spot a violation is you have a lot of similar if-else statements in a class

```
class Car:
    def __init__(self, brand):
        self.brand = brand
    def print_engine_type(self):
        if self.brand == 'Tesla Model 3':
            print('Electric')
        elif self.brand == 'Audi A3':
            print('Gasoline')
        elif self.brand == 'Myvi':
            print('Gasoline')

tesla_car = Car('Tesla Model 3')
audi_car = Car('Audi A3')
myvi_car = Car('Myvi')
for car in [tesla_car, audi_car, myvi_car]:
    car.print_engine_type()  # print Electric, Gasoline, Gasoline
```

```
from abc import ABC, abstractmethod
class Car(ABC):
    def __init__(self, brand):
        self.brand = brand
    @abstractmethod
    def print_engine_type(self):
        pass

class ElectricCar(Car):
    def print_engine_type(self):
        print('Electric')

class GasolineCar(Car):
    def print_engine_type(self):
        print('Gasoline')

class HybridCar(Car):
    def print_engine_type(self):
        print('Hybrid')

tesla_car = ElectricCar('Tesla Model 3')
audi_car = GasolineCar('Audi A3')
myvi_car = GasolineCar('Myvi')
jazz_car = HybridCar('Honda Jazz Hybrid')
for car in [tesla_car, audi_car, myvi_car, jazz_car]:
    car.print_engine_type()  # print Electric, Gasoline, Gasoline, Hybrid
```

## Liskov Substitution Principle

- If S is a subtype of T, then objects of type T may be replaced with objects of type S (i.e., an object of type T may be substituted with any object of a subtype S) without altering any of the desirable properties of the program (correctness, task performed, etc.)
- when you have a parent class (says Car) and a subclass (Tesla), any where you are using the parent class in code, you should be able to substitute it with the subclass without causing any issue.

```
class Car:
    def charge(self):
        pass
    def pump_petrol(self):
        pass

class Tesla(Car):
    def charge(self):
        print('Charging...')
    def pump_petrol(self):
        raise Exception('Not a gasoline car')

class AudiA3(Car):
    def charge(self):
        raise Exception('Not an electric car')
    def pump_petrol(self):
        print('Pumping...')
def recharge_car(car):
    car.charge()
tesla_car = Tesla()
audi_a3_car = AudiA3()
recharge_car(tesla_car)  # print Charging...
recharge_car(audi_a3_car)  # THIS RAISES EXCEPTION!
```

```
class Car:
    pass

class ElectricCar(Car):
    def charge(self):
        pass

class GasolineCar(Car):
    def pump_petrol(self):
        pass

class Tesla(ElectricCar):
    def charge(self):
        print('Charging...')

class AudiA3(GasolineCar):
    def pump_petrol(self):
        print('Pumping...')
def recharge_car(electric_car):
    electric_car.charge()

def pump_petrol(gasoline_car):
    gasoline_car.pump_petrol()
tesla_car = Tesla()
audi_a3_car = AudiA3()
recharge_car(tesla_car)  # print Charging...
pump_petrol(audi_a3_car)  # print('Pumping...')
```

## Interface Segregation Principle

- A class should not be forced to implement an interface that it does not use
- if the function is not needed by the subclass, we probably shouldn’t define that interface to be inherited in the parent class.

```
class Shape:
   """A demo shape class"""
   def draw_circle(self):
       """Draw a circle"""
       raise NotImplemented
   def draw_square(self):
       """ Draw a square"""
       raise NotImplemented
class Circle(Shape):
    """A demo circle class"""
   def draw_circle(self):
       """Draw a circle"""
       pass
   def draw_square(self):
       """ Draw a square"""
       pass
```

```
class Shape:
   """A demo shape class"""
   def draw(self):
      """Draw a shape"""
      raise NotImplemented
class Circle(Shape):
   """A demo circle class"""
   def draw(self):
      """Draw a circle"""
      pass
class Square(Shape):
   """A demo square class"""
   def draw(self):
      """Draw a square"""
      pass
```

## Dependency Inversion Principle

- High-level modules should not depend on low-level modules. Both should depend on abstractions.
- Abstractions should not depend on details. Details should depend on abstractions.
- Similar to API calls, changing the low-level implementation of an API shouldn’t impact the caller. A class shouldn’t need to understand the inner workings of another class for it to function properly.

```
class Garage:
    def __init__(self):
        self.cars = []
    def add_car_to_garage(self, name, capacity):
        self.cars.append((name, capacity))  # eg: ('Tesla1', 50)
class ChargingStation:
    def __init__(self, garage):
        num = len([car for car in garage.cars if car[1] < 50])
        print(f'We need to charge {num} car(s)')
gar = Garage()
gar.add_car_to_garage('Tesla1', 50)
gar.add_car_to_garage('Tesla2', 30)
gar.add_car_to_garage('Tesla1', 80)
station = ChargingStation(gar)  # print We need to charge 1 car(s)
```

```
from abc import ABC, abstractmethod
class Garage(ABC):
    def __init__(self):
        self.cars = []
    @abstractmethod
    def add_car_to_garage(self):
        pass
    @abstractmethod
    def get_cars_that_needs_charge(self):
        pass
class MyGarage(Garage):
    def add_car_to_garage(self, name, capacity):
        self.cars.append((name, capacity))
    def get_cars_that_needs_charge(self):
        return [car for car in self.cars if car[1] < 50]
class ChargingStation:
    def __init__(self, garage):
        num = len(garage.get_cars_that_needs_charge())
        print(f'We need to charge {num} car(s)')
gar = MyGarage()
gar.add_car_to_garage('Tesla1', 50)
gar.add_car_to_garage('Tesla2', 30)
gar.add_car_to_garage('Tesla3', 80)
station = ChargingStation(gar)  # print We need to charge 1 car(s)

class AnotherGarage(Garage):
    def add_car_to_garage(self, name, capacity):
        self.cars.append({'name': name, 'capacity': capacity})
    def get_cars_that_needs_charge(self):
        return [car for car in self.cars if car['capacity'] < 60]

gar = AnotherGarage()
gar.add_car_to_garage('Tesla1', 50)
gar.add_car_to_garage('Tesla2', 30)
gar.add_car_to_garage('Tesla3', 80)
station = ChargingStation(gar)  # print We need to charge 2 car(s)
# we don't need to modify implementation of ChargingStation
```

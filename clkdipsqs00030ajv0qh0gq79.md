---
title: "Design Patterns for Fun"
datePublished: Sat Jul 22 2023 04:36:29 GMT+0000 (Coordinated Universal Time)
cuid: clkdipsqs00030ajv0qh0gq79
slug: design-patterns-for-fun
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/chcyjyRQV74/upload/3da0b8bfa3b139f93784f8e355cf7ff7.jpeg
tags: design-patterns, software-development, python, software-engineering

---

Howdy Everyone! 🤠

> I always try to find a pattern while learning and implementing something. Patterns serve the predictability of operation, structure and behavior. For instance, If you know how academic writing structure works, you find it easy to predict what will possibly come next in the paragraph or paper.

Design patterns specifically are reusable solutions to common software design problems. They provide a structured and organized approach to designing software, making it more maintainable, scalable, and flexible.

These design patterns can be classified into 3 main groups based on purpose and application such as:

1. **Creational Design Patterns:** They focus on object creation mechanisms, providing ways to create objects in a manner suitable for a specific situation helping decouple the system from the details of object creation. For instance: Factory Method, Abstract Factory Method, Singleton, Builder, Prototype patterns.
    
2. **Structural Design Patterns:** They deal with object composition, providing ways to organize classes and objects to form larger structures and focus on simplifying the structure of the system. For instance: Adapter, Decorater, Facade, Composite, and Proxy patterns.
    
3. **Behavioral Design Patterns:** They focus on how objects interact and communicate with each other by defining communication patterns between classes. For instance: Command, Observer, Strategy, and Iterator patterns.
    

### Exploring Facade, Proxy and Command Patterns

#### A. Facade Pattern

We have been implementing facades knowingly/unknowingly all the time. The Facade pattern provides a unified interface to a complex subsystem, making it easier for clients to interact with the system by encapsulating the complexity and intricacies of the subsystem behind a simple API, shielding the client from the internal details.

Let us suppose: We have a multimedia system with an audio player and a video. Each component may have its various methods and configurations but we want to confine the component complexities within themselves.

```python
class AudioPlayer:
    def play_audio(self):
        print("Playing audio...")

class VideoPlayer:
    def play_video(self):
        print("Playing video...")

class MultimediaFacade:
    def __init__(self):
        self.audio_player = AudioPlayer()
        self.video_player = VideoPlayer()

    def play(self):
        self.audio_player.play_audio()
        self.video_player.play_video()

my_multimedia = MultimediaFacade()

# Turning my multimedia on 
my_multimedia.play()

# Playing audio...
# Playing video...
```

From the above code, we can see how the complex details of turning on of multimedia system are encapsulated by the `play()` method of `MultimediaFacade` class. The `MultimediaFacade` class acts as a facade for the multimedia system encapsulating individual components - `AudioPlayer`, `VideoPlayer`.

#### B. Proxy Pattern

The proxy pattern acts as a powerful tool to control access to objects and add additional functionalities without altering their core behavior. It is a structural design pattern that acts as a surrogate or placeholder for another object, allowing managing access to the real object and performing various tasks before or after its execution.

It is especially useful in scenarios where direct access to the real object is not desirable or when we need to add extra features, such as logging, access control, caching, or lazy initialization or when we want to prevent heavy resource utilization.

Let's consider a scenario: We have a Resource object that represents a heavy computational task in which we want to optimize the resource usage by caching its results.

```python
from abc import ABC, abstractmethod

class AbstractResource(ABC):
    @abstractmethod
    def execute_task(self, task_name):
        pass

class ConcreteResource(AbstractResource):
    def execute_task(self, task_name):
        print(f"Executing task '{task_name}'...")
        # Simulate heavy computational task here
        return f"Result of '{task_name}' task"

class ProxyResource:
    def __init__(self):
        self._real_resource = ConcreteResource()
        self._cache = {}

    def execute_task(self, task_name):
        if task_name in self._cache:
            print(f"Fetching cached result for task '{task_name}'...")
            return self._cache[task_name]
        else:
            result = self._real_resource.execute_task(task_name)
            self._cache[task_name] = result
            return result


if __name__ == "__main__":
    resource_proxy = ProxyResource()

    # First execution of the task (not in cache)
    task1_result = resource_proxy.execute_task("Task 1")
    print(task1_result)

    # Second execution of the same task (cached)
    task1_result_cached = resource_proxy.execute_task("Task 1")
    print(task1_result_cached)

    # Another task execution (not in cache)
    task2_result = resource_proxy.execute_task("Task 2")
    print(task2_result)
```

The `ProxyResource` class implements the `Resource` interface as a proxy for the real resource holding a reference to the real resource and maintaining a cache to store the results of executed tasks.

> Note: Implementing cache logic directly into `execute_task()` may not be a good approach but for simplicity. For cleaner implementation, cache hit or miss can be encapsulated in its own `Cache` class.

#### C. Command Pattern

The Command pattern is a behavioral design pattern that encapsulates a request as an object, allowing clients to parameterize and decouple the sender and receiver of a request. And what I mean by decoupling is the separation of concerns. Furthermore, we can implement undo/redo functionality, deferred execution, and logging of commands.

For exceptional reference regarding a more depth explanation of design patterns, look for the references below.

But for now:

Let's create a simple calculator application with the `Calculator` class, which supports basic operations like addition and subtraction as command objects.

```python
from abc import ABC, abstractmethod

class Command(ABC):
    @abstractmethod
    def execute(self):
        pass

class AddCommand(Command):
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def execute(self):
        return self.x + self.y

class SubtractCommand(Command):
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def execute(self):
        return self.x - self.y

class Calculator:
    def __init__(self):
        self.command = None

    def set_command(self,command):
        self.command = command

    def execute_command(self):
        return self.command.execute()

calculator = Calculator()

add_command = AddCommand(20, 5)
subtract_command = SubtractCommand(4, 2)

calculator.set_command(add_command)
result_add = calculator.execute_command()

print(result_add)

calculator.set_command(subtract_command)
result_subtract = calculator.execute_command()

print(result_subtract)
```

A single `execute_command()` method is called over `Calculator` class, which takes a `Command` object as an argument. Due to this, the execution output of the calculator is different based on the command that is passed to the instance of `Calculator`. Thus, we are now able to parameterize clients with different requests.

## Design Patterns: The Fun Part

Till now we have learned about 3 different design patterns, you'll be amazed to know the core reason for this blog is yet to come.

Problem: I have to hit 3rd party API (let it be Google search API), and respond to the client after processing, but I have to do it in a way that's more manageable, reliable and if we stack a new search API in our core, it should not hurt the overall code structuring.

Let's define an implementation class of our 3rd party API.

```python
from abc import ABC, abstractmethod
import requests
import os

class API(ABC):
    @abstractmethod
    def search(self, query):
        pass

class GenericSearchAPI(API):
    def __init__(self,url):
        self.url = url
    def search(self,query):
        pass

class GoogleAPI(GenericSearchAPI):
    def __init__(self,*args,**kwargs):
        self._access_key = os.getenv('access_key')
        super().__init__(self,*args,**kwargs)
    def search(self, query):
        res = requests.get(f"{self.url}/?search={query}&access_key={self._access_key}").json()
        return res
```

We want a domain linguistic for our search domain, our ubiquitous language. Let's suppose we are working in a finance company, we would like to call our search engine as `Finance Lookup` (I just made it up, pardon me ), rather than Google API, in the future we might implement local db search so we can't direct call GoogleAPI instance and call it as our holistic `Finance Lookup` engine.

**Using our knowledge of Facade:**

```python
from abc import ABC, abstractmethod
import requests
import os

class API(ABC):
    @abstractmethod
    def search(self, query):
        pass

class GenericSearchAPI(API):
    def __init__(self,url):
        self.url = url
    def search(self,query):
        pass

class GoogleAPI(GenericSearchAPI):
    def __init__(self,*args,**kwargs):
        self._access_key = os.getenv('access_key')
        super().__init__(self,*args,**kwargs)
    def search(self, query):
        res = requests.get(f"{self.url}/?search={query}&access_key={self._access_key}").json()
        return res

class FinanceLookup:
    def __init__(self):
        self.google_search = GoogleAPI()

    def lookup(self,keyword):
        results = self.google_search.search(keyword)
        return results

finance_lookup_engine = FinanceLookup()

keyword = "finance"
# Lookup keyword
results = finance_lookup_engine.lookup(keyword)
```

But Hold On.

Only if in our imaginary world, our 3rd party API would let us hit API continuously, but in reality, we are Throttled to a specific number of requests per unit of time. It seems better if we cache our responses to prevent hitting 3rd party API and causing delay/no responses at all.

Let's use our knowledge of using proxy patterns to either limit the user from our system or cache to reply them back, the response of exact query.

**Using our knowledge of Proxy:**

```python
from abc import ABC, abstractmethod
import requests
import os

class API(ABC):
    @abstractmethod
    def search(self, query):
        pass

class GenericSearchAPI(API):
    def __init__(self,url):
        self.url = url
    def search(self,query):
        pass

class GoogleAPI(GenericSearchAPI):
    def __init__(self,*args,**kwargs):
        self._access_key = os.getenv('access_key')
        super().__init__(self,*args,**kwargs)
    def search(self, query):
        res = requests.get(f"{self.url}/?search={query}&access_key={self._access_key}").json()
        return res

class GoogleAPIProxy:
    def __init__(self):
        self._real_api = GoogleAPI()
        self._cache = {}

    def search(self, query):
        if query in self._cache:
            return self._cache[query]
        else:
            result = self._real_api.search(query)
            self._cache[query] = result
            return result

class FinanceLookup:
    def __init__(self):
        self.google_search = GoogleAPIProxy()

    def lookup(self,keyword):
        results = self.google_search.search(keyword)
        return results

finance_lookup_engine = FinanceLookup()

keyword = "finance"
# Lookup keyword
results = finance_lookup_engine.lookup(keyword)
```

In the above code, `GoogleAPIProxy` is a proxy to `GoogleAPI` which searches the cache before hitting the actual API, to prevent excessive API hits.

Looks good till now right?

We have one more problem to be solved now. As I said earlier, we may have different search engines stacked to our `Finance Lookup`, what can we do to create a feature that allows searching using the next search engine without modifying the underlying code structure?

Our `Finance Lookup` is highly coupled with our `GoogleAPIProxy`. Let us give our users a choice to define which search engine to use.

```python
from abc import ABC, abstractmethod
import requests
import os

class API(ABC):
    @abstractmethod
    def search(self, query):
        pass

class GenericSearchAPI(API):
    def __init__(self,url):
        self.url = url
    def search(self,query):
        pass

class GoogleAPI(GenericSearchAPI):
    def __init__(self,*args,**kwargs):
        self._access_key = os.getenv('access_key')
        super().__init__(self,*args,**kwargs)
    def search(self, query):
        res = requests.get(f"{self.url}/?search={query}&access_key={self._access_key}").json()
        return res

class DuckDuckGOAPI(GenericSearchAPI):
    def __init__(self,*args,**kwargs):
        self._app_name = os.getenv('app_name')
        self._public_key = os.getenv('public_key')
        super().__init__(self,*args,**kwargs)
    def search(self, query):
        res = requests.get(f"{self.url}/?search={query}&app_name={self._app_name}&public_key={self._public_key}").json()
        return res

class SearchAPIProxy:
    def __init__(self,api):
        self._real_api = api
        self._cache = {}

    def search(self, query):
        if query in self._cache:
            return self._cache[query]
        else:
            result = self._real_api.search(query)
            self._cache[query] = result
            return result


class SearchCommand(ABC):
    @abstractmethod
    def execute(self, query):
        pass

class GoogleSearchCommand(SearchCommand):
    def __init__(self, api):
        self.api = api

    def execute(self, query):
        return self.api.search(query)

class DuckDuckGoSearchCommand(SearchCommand):
    def __init__(self, api):
        self.api = api

    def execute(self, query):
        return self.api.search(query)

class FinanceLookup:
    def __init__(self):
        self.google_search = SearchAPIProxy(GoogleAPI())
        self.duckduckgo_search = SearchAPIProxy(DuckDuckGOAPI())
        self.current_command = None

    def set_command(self, api_choice):
        if api_choice == "google":
            self.current_command = GoogleSearchCommand(self.google_search)
        elif api_choice == "duckduckgo":
            self.current_command = DuckDuckGoSearchCommand(self.duckduckgo_search)
        else:
            raise ValueError("Invalid API choice")

    def lookup(self,keyword):
        results = self.current_command.execute(keyword)
        return results

finance_lookup_engine = FinanceLookup()

api_choice = "google"

# Set the command based on the chosen API
finance_lookup_engine.set_command(api_choice)

# Lookup keyword using the selected API
keyword = "finance"
results = finance_lookup_engine.lookup(keyword)
print(results)
```

Phew !! A lot of code.

Let me summarize the changes I have made:

* Since Proxy behavior is needed for both 3rd party api's, rather than implementing a single Google Proxy now each instance of API's are supplied to the proxy while initializing. We have now `SearchAPIProxy` class.
    
* The command to execute APIs is segregated as `GoogleSearchCommand` and `DuckDuckGoSearchCommand` .
    
* Each command now executes the concrete implementation of the search APIs.
    

Well, that's done for now. I did it just for the sake of demonstrating how some patterns can be used to achieve code readability and future mutations/modifications.

**Harnessing the Power of Design Patterns:**

By incorporating design patterns into our coding practices, we can:

1. **Improve Code Quality**
    
2. **Reduce Code Duplication**
    
3. **Enhance Collaboration**
    
4. **Boost Flexibility and Adaptability**
    
5. **Ensure Best Practices**
    

**BUT...**

It's important to remember that design patterns are not one-size-fits-all solutions. Careful consideration must be given to the specific problem and context when choosing and implementing design patterns. Applying design patterns where they are not necessary or appropriate can lead to unnecessary complexity and reduced maintainability.

**Wrapping up**

Design patterns are powerful tools that can help create elegant, efficient, and scalable solutions to real-world problems. By understanding and applying design patterns, we can elevate our software designs and build robust and innovative software systems.

You reached the end of this blog, many thanks to you for taking the patience to follow along with me. Hope it helped in any way around.

### References:

* [https://refactoring.guru/design-patterns](https://refactoring.guru/design-patterns)
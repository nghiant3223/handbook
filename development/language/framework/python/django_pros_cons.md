# Pros and Cons of Django Framework for App Development
## Pros
### Batteries included
- Use readymade packages for adding functionalities instead of writing more code.
- For example: `authentication` with auth package, `admin interfacing` with admin package, `session management` with Sessions package and so on.
- Just import these packages and start building applications in no time.
### Flexible framework
- Written in `Python`, a `versatile programming language`, Django for web development provides more `flexibility` and `dynamism` to the developers.
- Excellent support for external libraries and packages. You can easily import a package in Django without any security concerns.
- `Explicit programming` rather than `implicit programming`, making it one of the ideal frameworks for applications that require rapid changes.
    - `Implicit`: the variable definition does not have to specify the type and when assigning/calculating variables of different types, there is no need to define the type to cast (for example: `JS`, but Go and Python are still `Explicit`)
    - `Explicit`: need to clarify the type of variable (for example: `Java`, `C++`, `Go`, `Python`)
### Faster development
- Enables faster development
- With the proper configuration, Django web development provides optimization for web applications.
- Allows developing multiple components at once, enabling rapid development.
### REST framework for APIs
- `Django REST framework` is a Python library which enables building APIs for the application, which provides powerful API functionality.
- The REST framework has a built-in API browser for testing API endpoints. Django web framework provides `authentication` and `permissions` rules to work flexibility with the framework.(https://www.django-rest-framework.org/topics/browsable-api/)
- Provides `customizations` that are not supported by other frameworks like `FLASK`
### Machine learning capabilities
- Compatible with some of the powerful `machine learning libraries` like `PyTorch`, `NumPy`, etc.
- Strong `computational` and `statistical` capabilities.
- Provides `regression` and `prediction` capabilities, enabling `intelligent application development`.
## Cons
### No conventions
- Doesn’t have `a set of principles` that developers can follow for web development.
### Not for smaller projects
- Not suitable for smaller projects and products with only a few features and requirements.
- Django calls for a lot of coding, which takes server processing time and bandwidth while development. It is generally used for projects which need scaling or are going to be launched on a large scale.
### Monolithic framework
- It encourages `big`, `tightly-coupled` apps.
- Everything depends on the `Django ORM`, whether it’s `front-end` stuff like sessions or `back-end` stuff like data persistence.
- All components get `deployed together`.
### Steep learning curve
- It is `difficult to learn`, comes with a lot of `features` and `configuration` that can’t be easily understood by developers.
- Has different syntax due to Python, and developers don’t get the hang of it quickly.
### Multiple request issue
- Unlike most web development frameworks, Django `can’t handle multiple requests simultaneously`.
- The reason Django doesn’t support multiple requests is that it encourages Django developers to `explore individual processes` and make decisions.
## Reference
- [Pros and Cons of Django Framework for App Development](https://dzone.com/articles/pros-and-cons-of-django-framework-for-app-developm)
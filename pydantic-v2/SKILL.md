---
name: pydantic-v2
description: Comprehensive expert skill for Pydantic V2. Covers BaseModel, Field definitions, custom validators (@field_validator, @model_validator), strict typing (Annotated, StringConstraints), model serialization (model_dump, model_validate), computed fields, JSON Schema, and model configuration (ConfigDict).
---

# Pydantic V2 Expert Skill

Expert guidance for data validation and settings management using **Pydantic V2** in Python. Pydantic V2 uses a Rust core (`pydantic-core`) for massive performance improvements and introduces new API methods (e.g., `model_dump` instead of `dict`).

## 1. Installation
```bash
pip install "pydantic>=2.0"
```

## 2. Basic Models & `Field`
Use `BaseModel` to define data schemas and `Field` to add metadata, default values, or constraints.

```python
from datetime import datetime
from pydantic import BaseModel, Field

class User(BaseModel):
    id: int
    name: str = Field(default="Anonymous", title="The user's name", max_length=50)
    signup_ts: datetime | None = None
    tags: list[str] = Field(default_factory=list)

# Instantiation
user = User(id=123, name="John Doe")
print(user.id) # 123
```

## 3. Serialization & Instantiation (V2 Methods)
In Pydantic V2, the V1 methods (`.dict()`, `.json()`, `.parse_obj()`) are deprecated. Use the new `model_*` methods.

### Instantiation / Parsing
```python
# From dict
user = User.model_validate({"id": 1, "name": "Jane"})

# From JSON string
json_data = '{"id": 2, "name": "Alice", "tags": ["admin"]}'
user = User.model_validate_json(json_data)
```

### Serialization / Exporting
```python
# To dictionary
user_dict = user.model_dump()
user_dict_exclude = user.model_dump(exclude={"tags"}, exclude_none=True)

# To JSON string
user_json = user.model_dump_json(indent=2)
```

## 4. Custom Validators
Pydantic V2 uses `@field_validator` and `@model_validator` instead of `@validator` and `@root_validator`.

### Field Validators
Use `@field_validator` to validate specific fields.
```python
from pydantic import BaseModel, field_validator, ValidationInfo

class Account(BaseModel):
    username: str
    
    @field_validator('username')
    @classmethod
    def username_alphanumeric(cls, v: str, info: ValidationInfo) -> str:
        if not v.isalnum():
            raise ValueError('username must be alphanumeric')
        return v.lower()
```

### Model Validators
Use `@model_validator(mode='after')` to validate dependencies between multiple fields.
```python
from pydantic import BaseModel, model_validator

class PasswordChange(BaseModel):
    password: str
    confirm_password: str

    @model_validator(mode='after')
    def check_passwords_match(self) -> 'PasswordChange':
        if self.password != self.confirm_password:
            raise ValueError('Passwords do not match')
        return self
```

## 5. Advanced Typing & Constraints
Use `typing.Annotated` combined with Pydantic's `Field` or `StringConstraints` for clean, reusable types.

```python
from typing import Annotated
from pydantic import BaseModel, Field, StringConstraints, conint

# Reusable annotated types
PositiveInt = Annotated[int, Field(gt=0)]
ZipCode = Annotated[str, StringConstraints(strip_whitespace=True, pattern=r'^\d{5}$')]

class Address(BaseModel):
    street: str
    zip_code: ZipCode
    house_number: PositiveInt
```

## 6. Computed Fields
If you want to include properties generated on the fly in your serialized output (JSON/dict), use `@computed_field`.

```python
from pydantic import BaseModel, computed_field

class Rectangle(BaseModel):
    width: float
    height: float

    @computed_field
    def area(self) -> float:
        return self.width * self.height

r = Rectangle(width=10, height=5)
print(r.model_dump()) # {'width': 10.0, 'height': 5.0, 'area': 50.0}
```

## 7. Model Configuration (`ConfigDict`)
Use `ConfigDict` instead of the old `class Config:` inner class to configure model behavior.

```python
from pydantic import BaseModel, ConfigDict

class StrictModel(BaseModel):
    # Forbid extra attributes, enforce strict typing, and allow alias population
    model_config = ConfigDict(
        extra='forbid', 
        strict=True,
        populate_by_name=True
    )
    
    first_name: str
    last_name: str
```

## 8. Aliases
Useful when Python attributes differ from the JSON payload (e.g., camelCase vs snake_case).

```python
from pydantic import BaseModel, Field, ConfigDict

class CamelCaseModel(BaseModel):
    model_config = ConfigDict(populate_by_name=True)
    
    first_name: str = Field(alias="firstName")
    
# Can be initialized via alias or attribute name
m = CamelCaseModel(firstName="John")
print(m.first_name) # "John"
print(m.model_dump(by_alias=True)) # {"firstName": "John"}
```

## 9. JSON Schema Generation
Pydantic can automatically generate OpenAPI-compliant JSON schemas.
```python
schema = User.model_json_schema()
print(schema)
```

## 10. Common Migration Pitfalls (V1 -> V2)
- Replace `.dict()` with `.model_dump()`.
- Replace `.json()` with `.model_dump_json()`.
- Replace `.parse_obj()` with `.model_validate()`.
- Replace `.parse_raw()` with `.model_validate_json()`.
- Replace `@validator` with `@field_validator`.
- Replace `@root_validator` with `@model_validator`.
- Replace `class Config:` with `model_config = ConfigDict(...)`.
- `BaseSettings` has been moved to a separate package: `pip install pydantic-settings`.

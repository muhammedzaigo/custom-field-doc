# Custom Fields Pydantic Schema Documentation

## Overview

This document provides detailed information about the Pydantic schemas used for validating, creating, and processing custom fields in the system. These schemas serve as the data transfer objects (DTOs) between the API layer and the database models.

## Table of Contents

- [Base Schemas](#base-schemas)
  - [BaseCreateOrUpdateCustomFieldModal](#basecreateoupdatecustomfieldmodal)
  - [BaseCustomFieldAttribute](#basecustomfieldattribute)
  - [BaseCustomFieldCreateModal](#basecustomfieldcreatemodal)
  - [BaseCustomFieldModal](#basecustomfieldmodal)
- [Validation Schemas](#validation-schemas)
  - [BaseValidationAttributes](#basevalidationattributes)
  - [BaseValidationValueModal](#basevalidationvaluemodal)
- [Usage Examples](#usage-examples)
  - [API Request Validation](#api-request-validation)
  - [Database to Schema Conversion](#database-to-schema-conversion)
  - [Schema to Database Conversion](#schema-to-database-conversion)
  - [Field Validation](#field-validation)
- [Best Practices](#best-practices)

## Base Schemas

### BaseCreateOrUpdateCustomFieldModal

A minimal schema used for creating or updating basic custom field information.

```python
class BaseCreateOrUpdateCustomFieldModal(BaseModel):
    """
    Schema for creating or updating a custom field.
    """
    label: str
    field_order: int
```

#### Attributes:

| Attribute | Type | Description |
|-----------|------|-------------|
| `label` | str | The display name of the custom field |
| `field_order` | int | The order in which the field appears |

#### Usage:

This schema is typically used for basic operations like changing a field's label or reordering fields.

### BaseCustomFieldAttribute

A schema representing an attribute of a custom field with value and disabled state.

```python
class BaseCustomFieldAttribute(BaseModel):
    value: bool
    disabled: bool
```

#### Attributes:

| Attribute | Type | Description |
|-----------|------|-------------|
| `value` | bool | The boolean value of the attribute |
| `disabled` | bool | Whether the attribute can be modified |

#### Usage:

This schema is used to represent boolean attributes that can be enabled/disabled, such as the "required" flag on a field.

### BaseCustomFieldCreateModal

An extended schema for creating new custom fields with all configurable options.

```python
class BaseCustomFieldCreateModal(BaseModel):
    """
    Schema for returning a custom field response.
    """
    customfield_id: UUID4
    label: str
    field_type: Optional[CustomFieldType] = None
    required: BaseCustomFieldAttribute
    unique: BaseCustomFieldAttribute
    show_or_hide: BaseCustomFieldAttribute
    field_order: int
    placeholder: Optional[str] = None
    min_length: Optional[int] = None
    max_length: Optional[int] = None
    regex: Optional[str] = ""
    min_value: Optional[float] = None
    max_value: Optional[float] = None
    options: Optional[List[Dict]] = []
    file_size: Optional[int] = None
    file_types: Optional[List] = []
```

#### Attributes:

| Attribute | Type | Description |
|-----------|------|-------------|
| `customfield_id` | UUID4 | Unique identifier for the custom field |
| `label` | str | Display name shown to users |
| `field_type` | Optional[CustomFieldType] | Type of the field (TEXT, NUMBER, EMAIL, etc.) |
| `required` | BaseCustomFieldAttribute | Whether the field is mandatory and if this attribute can be modified |
| `unique` | BaseCustomFieldAttribute | Whether values must be unique and if this attribute can be modified |
| `show_or_hide` | BaseCustomFieldAttribute | Controls field visibility and if this attribute can be modified |
| `field_order` | int | Position in display order |
| `placeholder` | Optional[str] | Placeholder text for empty fields |
| `min_length` | Optional[int] | Minimum text length (for text types) |
| `max_length` | Optional[int] | Maximum text length (for text types) |
| `regex` | Optional[str] | Validation pattern (for text types) |
| `min_value` | Optional[float] | Minimum value (for numeric types) |
| `max_value` | Optional[float] | Maximum value (for numeric types) |
| `options` | Optional[List[Dict]] | List of options for dropdown, radio button, etc. |
| `file_size` | Optional[int] | Maximum file size in bytes |
| `file_types` | Optional[List] | List of allowed file extensions |

#### Usage:

This schema is used for creating new custom fields with all available configuration options. Note that boolean attributes (required, unique, show_or_hide) use the `BaseCustomFieldAttribute` type which includes both the value and whether it can be changed.

### BaseCustomFieldModal

A schema for representing existing custom fields in API responses.

```python
class BaseCustomFieldModal(BaseModel):
    """
    Schema for returning a custom field response.
    """
    customfield_id: UUID4
    label: str
    field_type: Optional[CustomFieldType] = None
    required: bool
    unique: bool
    show_or_hide: bool
    field_order: int
    placeholder: Optional[str] = None
    min_length: Optional[int] = None
    max_length: Optional[int] = None
    regex: Optional[str] = ""
    min_value: Optional[float] = None
    max_value: Optional[float] = None
    options: Optional[List[Dict]] = []
    file_size: Optional[int] = None
    file_types: Optional[List] = []
```

#### Attributes:

| Attribute | Type | Description |
|-----------|------|-------------|
| `customfield_id` | UUID4 | Unique identifier for the custom field |
| `label` | str | Display name shown to users |
| `field_type` | Optional[CustomFieldType] | Type of the field (TEXT, NUMBER, EMAIL, etc.) |
| `required` | bool | Whether the field is mandatory |
| `unique` | bool | Whether values must be unique |
| `show_or_hide` | bool | Controls field visibility |
| `field_order` | int | Position in display order |
| `placeholder` | Optional[str] | Placeholder text for empty fields |
| `min_length` | Optional[int] | Minimum text length (for text types) |
| `max_length` | Optional[int] | Maximum text length (for text types) |
| `regex` | Optional[str] | Validation pattern (for text types) |
| `min_value` | Optional[float] | Minimum value (for numeric types) |
| `max_value` | Optional[float] | Maximum value (for numeric types) |
| `options` | Optional[List[Dict]] | List of options for dropdown, radio button, etc. |
| `file_size` | Optional[int] | Maximum file size in bytes |
| `file_types` | Optional[List] | List of allowed file extensions |

#### Usage:

This schema is similar to `BaseCustomFieldCreateModal` but uses simple boolean values for required, unique, and show_or_hide attributes instead of the `BaseCustomFieldAttribute` type. It's typically used for API responses where we just want to show the current state without metadata about whether the attributes can be modified.

## Validation Schemas

### BaseValidationAttributes

A schema representing validation rules and constraints for a custom field.

```python
class BaseValidationAttributes(BaseModel):
    required: bool = False
    unique: bool = False
    read_only: bool = False
    is_deleted_file: bool = False
    options: Optional[List[Dict]] = None
    file_size: Optional[int] = None
    file_types: Optional[List[str]] = None
```

#### Attributes:

| Attribute | Type | Description |
|-----------|------|-------------|
| `required` | bool | Whether the field is mandatory |
| `unique` | bool | Whether values must be unique |
| `read_only` | bool | Whether the field is read-only |
| `is_deleted_file` | bool | Whether a file has been deleted (for file fields) |
| `options` | Optional[List[Dict]] | Available options for selection fields |
| `file_size` | Optional[int] | Maximum file size in bytes |
| `file_types` | Optional[List[str]] | List of allowed file extensions |

#### Usage:

This schema is used to define validation rules and constraints for a custom field, particularly when validating input data against field definitions.

### BaseValidationValueModal

A schema for validating and processing custom field values.

```python
class BaseValidationValueModal(BaseModel):
    label: Optional[str] = None
    field_type: Optional[CustomFieldType] = None
    value: Union[str, int, UUID4, List, Any, None] = None
    field_id: Optional[UUID4] = None
    attributes: Optional[BaseValidationAttributes]
```

#### Attributes:

| Attribute | Type | Description |
|-----------|------|-------------|
| `label` | Optional[str] | The display name of the field |
| `field_type` | Optional[CustomFieldType] | Type of the field |
| `value` | Union[str, int, UUID4, List, Any, None] | The actual value of the field |
| `field_id` | Optional[UUID4] | Unique identifier of the custom field |
| `attributes` | Optional[BaseValidationAttributes] | Validation rules and constraints |

#### Usage:

This schema is used when validating and processing values submitted for custom fields. It combines the field metadata with the submitted value and validation attributes.

## Usage Examples

### API Request Validation

```python
from fastapi import APIRouter, Depends, HTTPException
from pydantic import UUID4
from typing import List

router = APIRouter()

@router.post("/custom-fields/")
async def create_custom_field(field: BaseCustomFieldCreateModal):
    """Create a new custom field."""
    # The request body is automatically validated against the schema
    try:
        # Convert Pydantic model to database model
        db_field = CustomField(
            label=field.label,
            field_type=field.field_type,
            required=field.required.value,
            unique=field.unique.value,
            show_or_hide=field.show_or_hide.value,
            field_order=field.field_order,
            # ... other fields
        )
        # Save to database
        db.add(db_field)
        db.commit()
        return {"id": db_field.id}
    except Exception as e:
        raise HTTPException(status_code=400, detail=str(e))

@router.put("/custom-fields/{field_id}")
async def update_custom_field(
    field_id: UUID4, 
    update_data: BaseCreateOrUpdateCustomFieldModal
):
    """Update a custom field's basic properties."""
    field = db.query(CustomField).get(field_id)
    if not field:
        raise HTTPException(status_code=404, detail="Field not found")
    
    field.label = update_data.label
    field.field_order = update_data.field_order
    db.commit()
    return {"success": True}
```

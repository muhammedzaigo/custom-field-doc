# Custom Field Enums Documentation

## Overview

This document provides detailed information about custom field type and status enumerations used in the system. These enumerations define the various types of custom fields that can be created and their possible statuses.

## Table of Contents

- [CustomFieldType](#customfieldtype)
- [CustomFieldStatus](#customfieldstatus)
- [Usage Examples](#usage-examples)
- [Best Practices](#best-practices)

## CustomFieldType

`CustomFieldType` is an enumeration class that defines all possible types of custom fields available in the system. Each field type has specific behavior and validation rules.

```python
class CustomFieldType(Enum):
    TEXT = "text"                       # A standard text input field
    NUMBER = "number"                   # A numeric input field
    EMAIL = "email"                     # An email input field with validation
    PHONE = "phone"                     # A phone number input field
    AMOUNT = "amount"                   # A field for entering monetary values
    TEXTAREA = "textarea"               # A multi-line text input field
    SINGLE_DROPDOWN = "single_dropdown" # A dropdown field allowing a single selection
    MULTIPLE_DROPDOWN = "multiple_dropdown" # A dropdown field allowing multiple selections
    TAG = "tag"                         # A tag field
    RADIO = "radio"                     # A radio button selection field
    CHECKBOX = "checkbox"               # A checkbox selection field
    DATE = "date"                       # A date input field
    TIME = "time"                       # A time input field
    FILE = "file"                       # A file upload field
    IMAGE = "image"                     # An image upload field
    MULTIPLE_FILE = "multiple_file"     # Multiple file upload field.
    MULTIPLE_IMAGE = "multiple_image"   # Multiple image upload field.
    LOCATION = "location"               # Location input field, possibly using coordinates or map picker.
```

### Field Type Descriptions

| Type | Description | Validation | Example Use Case |
|------|-------------|------------|------------------|
| `TEXT` | Single-line text input | None | Names, short descriptions |
| `NUMBER` | Numeric value input | Numeric validation | Age, quantity, score |
| `EMAIL` | Email address input | Email format validation | Contact information |
| `PHONE` | Phone number input | Phone format validation | Contact information |
| `AMOUNT` | Monetary value input | Numeric with decimal validation | Prices, costs, budgets |
| `TEXTAREA` | Multi-line text input | None | Long descriptions, comments |
| `SINGLE_DROPDOWN` | Single selection dropdown | Must be one of predefined options | Categories, status selection |
| `MULTIPLE_DROPDOWN` | Multiple selection dropdown | Must be from predefined options | Tags, multi-category selection |
| `TAG` | Tag selection field | None | Keywords, categorization |
| `RADIO` | Radio button selection | Must be one of predefined options | Single-choice questions |
| `CHECKBOX` | Checkbox selection | Boolean or multiple selection | Feature toggles, multi-select |
| `DATE` | Date input | Date format validation | Deadlines, birthdates |
| `TIME` | Time input | Time format validation | Appointment times |
| `FILE` | Single file upload | File type validation | Document uploads |
| `IMAGE` | Single image upload | Image type validation | Profile pictures |
| `MULTIPLE_FILE` | Multiple file upload | File type validation | Document collections |
| `MULTIPLE_IMAGE` | Multiple image upload | Image type validation | Product galleries |
| `LOCATION` | Location input | Coordinate validation | Stores, event venues |

## CustomFieldStatus

`CustomFieldStatus` is an enumeration class that defines the possible statuses a custom field can have in the system.

```python
class CustomFieldStatus(str, Enum):
    DELETED = "deleted"   # The field has been deleted
    ACTIVE = "active"     # The field is active and available for use
    INACTIVE = "inactive" # The field is inactive and not currently in use
    
    _labels = {
        "deleted": "Deleted",
        "active": "Active",
        "inactive": "Inactive",
    }
```

### Status Descriptions

| Status | Display Label | Description | 
|--------|--------------|-------------|
| `DELETED` | "Deleted" | Field is marked as deleted and should not be displayed to users or used for data collection. It may be retained in the database for historical data integrity. |
| `ACTIVE` | "Active" | Field is currently active and should be displayed to users for data input or display. |
| `INACTIVE` | "Inactive" | Field is temporarily disabled but not deleted. It should not be displayed to users but can be reactivated later. |

## Usage Examples

### Basic Usage

```python
from custom_fields.enums import CustomFieldType, CustomFieldStatus

# Creating a new custom field
new_field = {
    "name": "Email Address",
    "type": CustomFieldType.EMAIL.value,
    "status": CustomFieldStatus.ACTIVE.value,
    "required": True
}

# Checking field type
if new_field["type"] == CustomFieldType.EMAIL.value:
    # Apply email validation rules
    pass

# Checking field status
if new_field["status"] == CustomFieldStatus.ACTIVE.value:
    # Display field in the form
    pass
```

### Working with Status Labels

```python
from custom_fields.enums import CustomFieldStatus

# Get the display label for a status
status_value = "active"
display_label = CustomFieldStatus._labels.get(status_value, status_value)
# display_label will be "Active"

# Display all available statuses with their labels
all_statuses = [(status.value, CustomFieldStatus._labels.get(status.value)) 
                for status in CustomFieldStatus]
# Returns: [("deleted", "Deleted"), ("active", "Active"), ("inactive", "Inactive")]
```

### Type Checking

```python
from custom_fields.enums import CustomFieldType

def validate_field_value(field_type, value):
    """
    Validate a value based on the field type
    """
    if field_type == CustomFieldType.EMAIL.value:
        # Email validation logic
        import re
        email_pattern = r'^[\w\.-]+@[\w\.-]+\.\w+$'
        return bool(re.match(email_pattern, value))
    elif field_type == CustomFieldType.NUMBER.value:
        # Number validation logic
        try:
            float(value)
            return True
        except ValueError:
            return False
    # Additional validation for other types
    return True
```

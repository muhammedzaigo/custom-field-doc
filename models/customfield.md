## Table of Contents

- [System Architecture](#system-architecture)
- [Base Models](#base-models)
  - [BaseModal](#basemodal)
  - [BaseCustomField](#basecustomfield)
  - [BaseCustomFieldOption](#basecustomfieldoption)
  - [BaseCustomFieldValue](#basecustomfieldvalue)
- [Enumerations](#enumerations)
  - [CustomFieldType](#customfieldtype)
  - [CustomFieldStatus](#customfieldstatus)
- [Usage Examples](#usage-examples)
- [Best Practices](#best-practices)

## System Architecture

The custom fields system is designed to provide flexibility in defining, configuring, and storing various types of custom fields across different modules. The architecture follows a layered approach:

1. **Base Layer**: Provides common attributes for all models (`BaseModal`)
2. **Custom Field Definitions Layer**: Defines field structure and validation (`BaseCustomField`)
3. **Options Layer**: Stores predefined options for selection fields (`BaseCustomFieldOption`)
4. **Values Layer**: Stores actual data entered for the custom fields (`BaseCustomFieldValue`)

This architecture allows for:
- Strong typing and validation
- Flexible field configurations
- Efficient querying and indexing
- Clear separation of field definitions and values

### Class Relationships

```
BaseModal
   ↑
   ├── BaseCustomField
   ├── BaseCustomFieldOption
   └── BaseCustomFieldValue
```

## Base Models

### BaseModal

`BaseModal` serves as the abstract base class that provides common attributes for all models in the system:

```python
class BaseModal:
    """
    Abstract base model providing common attributes for all models in the custom field system.
    """
    __abstract__ = True
    
    id = Column(UUID(as_uuid=True), primary_key=True, index=True, default=uuid.uuid4)
    status = Column(Enum(CustomFieldStatus), default=CustomFieldStatus.ACTIVE)
    created_at = Column(DateTime, default=func.current_timestamp())
    updated_at = Column(
        DateTime, default=func.current_timestamp(), onupdate=func.current_timestamp()
    )
    created_by = Column(UUID(as_uuid=True), nullable=True)
    updated_by = Column(UUID(as_uuid=True), nullable=True)
```

#### Key Attributes:

| Attribute | Type | Description |
|-----------|------|-------------|
| `id` | UUID | Primary key uniquely identifying each record |
| `status` | CustomFieldStatus | Current status of the record (ACTIVE, INACTIVE, DELETED) |
| `created_at` | DateTime | Timestamp when the record was created |
| `updated_at` | DateTime | Timestamp when the record was last updated |
| `created_by` | UUID | Reference to the user who created the record |
| `updated_by` | UUID | Reference to the user who last updated the record |

### BaseCustomField

`BaseCustomField` extends `BaseModal` and provides the structure for defining custom fields:

```python
class BaseCustomField(BaseModal):
    """
    Base model for defining custom fields.
    """
    __abstract__ = True
    
    label = Column(String(255), default="Untitled", nullable=False, index=True)
    field_type = Column(Enum(CustomFieldType), default=CustomFieldType.TEXT, nullable=True)
    required = Column(Boolean, default=False)
    show_or_hide = Column(Boolean, default=True)
    unique = Column(Boolean, default=False)
    field_order = Column(Integer, default=0, nullable=True)
    placeholder = Column(String(255), default=None, nullable=True)
    min_length = Column(Integer, default=None, nullable=True)
    max_length = Column(Integer, default=None, nullable=True)
    regex = Column(String(255), default=None, nullable=True)
    min_value = Column(Float, default=None, nullable=True)
    max_value = Column(Float, default=None, nullable=True)
    file_size = Column(Integer, default=None, nullable=True)
    file_types = Column(JSON, default=[], nullable=True)
    
    __table_args__ = (
        Index("idx_customfield_field_type", "field_type"),
        Index("idx_customfield_label", "label"),
    )
```

#### Key Attributes:

| Attribute | Type | Description |
|-----------|------|-------------|
| `label` | String | Display label shown to users |
| `field_type` | CustomFieldType | Type of field (TEXT, NUMBER, EMAIL, etc.) |
| `required` | Boolean | Whether the field is mandatory |
| `show_or_hide` | Boolean | Controls field visibility |
| `unique` | Boolean | Whether values must be unique |
| `field_order` | Integer | Position in display order |
| `placeholder` | String | Placeholder text for empty fields |
| `min_length` | Integer | Minimum text length (for text types) |
| `max_length` | Integer | Maximum text length (for text types) |
| `regex` | String | Validation pattern (for text types) |
| `min_value` | Float | Minimum value (for numeric types) |
| `max_value` | Float | Maximum value (for numeric types) |
| `file_size` | Integer | Maximum file size in bytes |
| `file_types` | JSON | List of allowed file extensions |

### BaseCustomFieldOption

`BaseCustomFieldOption` extends `BaseModal` and stores predefined options for selection fields:

```python
class BaseCustomFieldOption(BaseModal):
    """
    Base model for storing custom field options.
    """
    __abstract__ = True
    
    option = Column(String(255), nullable=False)
    
    __table_args__ = (Index("idx_customfield_option", "option"),)
```

#### Key Attributes:

| Attribute | Type | Description |
|-----------|------|-------------|
| `option` | String | The option value for dropdown/selection fields |

### BaseCustomFieldValue

`BaseCustomFieldValue` extends `BaseModal` and stores the actual values entered for custom fields:

```python
class BaseCustomFieldValue(BaseModal):
    """
    Base model for storing values entered for custom fields.
    """
    __abstract__ = True
    
    value = Column(Text, nullable=True)
    value_json = Column(JSON, default=[], nullable=True)
    value_date = Column(Date, nullable=True)
    value_time = Column(Time, nullable=True)
    
    __table_args__ = (
        Index("idx_customfield_value", "value"),
        Index("idx_customfield_value_date", "value_date"),
        Index("idx_customfield_value_time", "value_time"),
    )
```

#### Key Attributes:

| Attribute | Type | Description |
|-----------|------|-------------|
| `value` | Text | Simple text value for the field |
| `value_json` | JSON | Complex data stored as JSON |
| `value_date` | Date | Date value for date fields (for filtering/sorting) |
| `value_time` | Time | Time value for time fields (for filtering/sorting) |

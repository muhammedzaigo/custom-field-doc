
# 📌 Custom Field Service Documentation

## 🔧 Core Methods

### ✅ Custom Field Operations

| Method                                | Description                            | Parameters                                                                   | Return Type              |
|---------------------------------------|----------------------------------------|------------------------------------------------------------------------------|--------------------------|
| `create_custom_field`                 | Creates a new custom field             | `customfield_basemodal`, `customfield_entity_id`, `user_id`                | `Tuple[UUID4, int]`      |
| `update_custom_field_type`           | Updates field type                     | `customfield_id`, `field_type`, `user_id`                                   | `Tuple[str, int]`        |
| `update_custom_field_labal`          | Updates field label                    | `customfield_id`, `label`, `user_id`                                        | `Tuple[str, int]`        |
| `update_custom_field_required`       | Sets if field is required              | `customfield_id`, `required`, `user_id`                                     | `Tuple[str, int]`        |
| `update_custom_field_unique`         | Sets if field values must be unique    | `customfield_id`, `unique`, `user_id`                                       | `Tuple[str, int]`        |
| `update_custom_field_show_or_hide`   | Shows or hides field                   | `customfield_id`, `visibility`, `user_id`                                   | `Tuple[str, int]`        |
| `update_custom_field_file_attributes`| Updates file size and types            | `customfield_id`, `user_id`, `file_size`, `file_types`                      | `Tuple[str, int]`        |
| `update_custom_fields_reorder`       | Reorders multiple fields               | `customfield_entity_id`, `field_order_map`, `user_id`                       | `Tuple[str, int]`        |
| `delete_custom_field`                | Soft deletes a field                   | `customfield_id`, `user_id`                                                 | `Tuple[str, int]`        |

### 🔍 Custom Field Retrieval

| Method                                              | Description                                | Parameters                                     | Return Type                            |
|-----------------------------------------------------|--------------------------------------------|------------------------------------------------|----------------------------------------|
| `retrieve_custom_field`                             | Gets a single field                         | `customfield_id`                               | `Tuple[BaseCustomFieldCreateModal, int]` |
| `retrieve_all_custom_fields`                        | Gets all fields for an entity               | `customfield_entity_id`                        | `Tuple[List[BaseCustomFieldCreateModal], int]` |
| `retrieve_visible_custom_fields`                    | Gets visible fields for an entity           | `customfield_entity_id`                        | `Tuple[List[BaseCustomFieldModal], int]` |
| `retrieve_selected_custom_field_attributes`         | Gets specific field attributes              | `customfield_entity_id`, `return_fields`       | `Tuple[List[Dict], int]`              |
| `retrieve_custom_field_labels_and_types_by_customfield_id` | Gets field labels and types          | `customfield_entity_id`                        | `Dict[UUID4, Dict]`                    |

### 🧩 Custom Field Option Operations

| Method                        | Description                 | Parameters                                    | Return Type              |
|-------------------------------|-----------------------------|-----------------------------------------------|--------------------------|
| `create_custom_field_option`  | Creates a new option        | `customfield_id`, `option`, `user_id`         | `Tuple[str, int]`        |
| `retrieve_custom_field_options` | Gets options for a field  | `customfield_id`                              | `Tuple[List[Dict], int]` |
| `retrieve_custom_fields_options` | Gets options for all fields | `customfield_entity_id`                    | `Tuple[Dict[str, List[Dict]], int]` |
| `update_custom_field_option` | Updates an option           | `customfield_id`, `option_id`, `option`, `user_id` | `Tuple[str, int]`        |
| `delete_custom_field_option` | Soft deletes an option      | `customfield_id`, `option_id`, `user_id`      | `Tuple[str, int]`        |

### 📝 Custom Field Value Operations

| Method                         | Description                 | Parameters                                           | Return Type                 |
|--------------------------------|-----------------------------|------------------------------------------------------|-----------------------------|
| `update_custom_fields_values`  | Updates multiple field values| `customfield_value_entity_id`, `custom_fields`, `updated_by` | `Tuple[str, List[str], int]` |
| `update_custom_field_value`    | Updates a single field value| `customfield_value_entity_id`, `custom_field`, `updated_by` | `Tuple[str, List[str], int]` |

---

## 🚀 Usage Examples

### ✅ Creating a Custom Field Service Instance

```python
from sqlalchemy.ext.asyncio import AsyncSession
from my_models import CustomField, CustomFieldOption, CustomFieldValue
from custom_field.services import CustomFieldService

async def create_service(db: AsyncSession) -> CustomFieldService:
    return CustomFieldService(
        CustomField_Model=CustomField,
        CustomFieldOption_Model=CustomFieldOption,
        CustomFieldValue_Model=CustomFieldValue,
        db=db
    )
```


### Creating a Custom Field

```python
from uuid import UUID
from custom_field.schemas.customfield import BaseCreateOrUpdateCustomFieldModal

async def create_field(
    db: AsyncSession, 
    entity_id: UUID, 
    user_id: UUID, 
    field_data: BaseCreateOrUpdateCustomFieldModal
) -> UUID:
    service = await create_service(db)
    msg_or_error, status = await service.create_custom_field(
        customfield_basemodal=field_data,
        customfield_entity_id=entity_id,
        user_id=user_id
    )
    
    if status != 200:
        raise BadRequest(detail=msg_or_error)
    
    return msg_or_error
```


### Retrieving Custom Fields

```python
async def get_all_fields(db: AsyncSession, entity_id: UUID):
    service = await create_service(db)
    fields, status = await service.retrieve_all_custom_fields(entity_id)
    
    if status != 200:
        raise Exception(f"Failed to retrieve custom fields: {fields}")
    
    return fields
```

### Managing Field Options

```python
async def add_option(db: AsyncSession, field_id: UUID, option_text: str, user_id: UUID):
    service = await create_service(db)
    result, status = await service.create_custom_field_option(
        customfield_id=field_id,
        option=option_text,
        user_id=user_id
    )
    
    if status != 200:
        raise Exception(f"Failed to add option: {result}")
    
    return result
```

### Updating Field Values

```python
from custom_field.schemas.customfield import BaseValidationValueModal

async def update_field_value(
    db: AsyncSession, 
    entity_id: UUID, 
    field_value: BaseValidationValueModal, 
    user_id: UUID
):
    service = await create_service(db)
    result, deleted_files, status = await service.update_custom_field_value(
        customfield_value_entity_id=entity_id,
        custom_field=field_value,
        updated_by=user_id
    )
    
    if status != 200:
        raise Exception(f"Failed to update field value: {result}")
    
    # Handle deleted files if needed
    if deleted_files:
        # Process deleted files (e.g., remove from storage)
        pass
    
    return result
```

### Updating Field Values

```python
from custom_field.schemas.customfield import BaseValidationValueModal

async def update_field_value(
    db: AsyncSession, 
    entity_id: UUID, 
    field_value: BaseValidationValueModal, 
    user_id: UUID
):
    service = await create_service(db)
    result, deleted_files, status = await service.update_custom_field_value(
        customfield_value_entity_id=entity_id,
        custom_field=field_value,
        updated_by=user_id
    )
    
    if status != 200:
        raise Exception(f"Failed to update field value: {result}")
    
    # Handle deleted files if needed
    if deleted_files:
        # Process deleted files (e.g., remove from storage)
        pass
    
    return result
```

### Error Handling

All service methods return a tuple with the result (or error message) and a status code. Successful operations return status code 200. Common error codes include:

- 400: Bad request (invalid input)
- 404: Not found (custom field or option not found)
- 500: Database error or unexpected exception

---
title: "在Drupal9 中增加字段长度而不丢失数据"
description: ""
summary: "在这篇文章中，我将向您展示如何在 drupal中增加字段长度而不丢失数据。"
date: "2023-11-03T16:25:08+08:00"
thumbnail: ""
draft: false
categories:
  - "Develop"
tags:
  - "Drupal"
---
## Introduction
今天遇到一个需要在Drupal中修改已有字段的长度，将一个String字段默认长度255，修改为1000。

### Solution
使用 hook_update_N() 在数据库、配置和存储模式中将字段的长度更改为 255。像下面的例子。

```
<?php

use Drupal\field\Entity\FieldStorageConfig;

/**
 * @param $sandbox
 */
function mymodule_update_90005(&$sandbox) {
  $entity_type = 'node';
  $field_name = 'field_description';
  $field_length = 255;

  $database = \Drupal::database();
  // Resize the main field data table.
  $database->query("ALTER TABLE {$entity_type}__{$field_name} MODIFY {$field_name}_value VARCHAR({$field_length})");
  // Resize the revision field data table.
  $database->query("ALTER TABLE {$entity_type}_revision__{$field_name} MODIFY {$field_name}_value VARCHAR({$field_length})");

  // Update storage schema.
  $storage_key = $entity_type . '.field_schema_data.' . $field_name;
  $storage_schema = \Drupal::keyValue('entity.storage_schema.sql');
  $field_schema = $storage_schema->get($storage_key);
  $field_schema[$entity_type . '__' . $field_name]['fields'][$field_name . '_value']['length'] = $field_length;
  $field_schema[$entity_type . '_revision__' . $field_name]['fields'][$field_name . '_value']['length'] = $field_length;
  $storage_schema->set($storage_key, $field_schema);

  // Update field configuration.
  $config = \Drupal::configFactory()
    ->getEditable("field.storage.{$entity_type}.{$field_name}");
  $config->set('settings.max_length', $field_length);
  $config->save(TRUE);

  // Update field storage configuration.
  FieldStorageConfig::loadByName($entity_type, $field_name)->save();

  return t('Length of @entity-type.@field-name updated to @field-length', [
    '@entity-type' => $entity_type,
    '@field-name' => $field_name,
    '@field-length' => $field_length,
  ]);
}
```
---
**Reference**
- [https://codimth.com/blog/web/drupal/increase-length-field-without-losing-data-drupal-8 ](https://codimth.com/blog/web/drupal/increase-length-field-without-losing-data-drupal-8 )


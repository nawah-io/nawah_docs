[Back to Index](/README.md)

# ATTR
`ATTR` is the controller in Nawah which is used to define `Attrs Types` in [Nawah Modules](/reference/module.md). `Attrs Types` are used to automate the process of data validation for Nawah apps. In most it's used with [calls `doc`](/reference/method.md#doc) to methods, but also can be used to validate [GET methods `query`](/reference/method.md#get-methods).

## Built-in Attrs Types
Nawah has huge set of built-in `attrs` types that cover most of the data validation purposes. These Attrs Types can be customised for more specific validation process.

All `attrs` types can be defined by using the helper methods in `ATTR` controller of the same type name.

## `ANY`
An attr with this type means it can have any value, ultimately skipping type-check on it altogether.

## `ACCESS`
An attr with this type defines a [`DICT`](/api/ATTR.md#dict)-type with attributes `users` a list of `ObjectId` referring to users who have access to specific doc, `groups` a list of `ObjectId` referring to groups who have access to specific doc, `anon` a boolean set to `True` or `False` to refer whether anonymous users have access to specific doc.

## `ID`
An attr with this type means it has to have `ObjectId`. This is usually the type set for attr that has the `_id` value of another doc from the same module or another. Nawah type-check step for this type includes converting `str` type to `ObjectId` if valid, allowing developers to pass `str` values to `attrs` with type `ID`.

## `STR`
Self-descriptive string type. Accepts anything that is a string.

### `STR`:pattern
Option `pattern` of type `STR` allows developers to force custom pattern check by passing a Python format regexp.

## `INT`
Self-descriptive integer type. Accepts anything that is an integer.

### `INT`:range
Option `range` of type `INT` allows developers to force custom range check by passing a list of integers referring to Python range in format `[BEGIN, END[, STEP]]`, meaning `BEGIN` is inclusive, but `END` is exclusive. e.g. `[1, 10]` matches anything from inclusive `1` to exclusive `10`, and `[0, 11, 2]` matches all even numbers from `1` to `10` all-inclusive.

## `BOOL`
Self-descriptive boolean type. Accepts anything that is a boolean.

## `LOCALE`
An attr with this type means it's Python `dict` with multiple values, each representing a supported locale in Nawah app.

## `LOCALES`
An attr with this type means it's a `str` with the accepted value being one of the supported locales in Nawah app.

## `EMAIL`
Self-descriptive email type. Accepts a string that matches the regexp `^[^@]+@[^@]+\.[^@]+$` in Python.

## `PHONE`
Self-descriptive phone type. Accepts a string that matches the regexp `^\+[0-9]+` in Python.
HERE HERE HERE

### `PHONE`:codes
Option `codes` of type `PHONE` allows developers to set specific accepted codes for attr in form of list of codes. e.g. `['971', '966', '1']`

## `IP`
Self-descriptive IP[v4] type. Accepts a string that matches the regexp `^(?:(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$` in Python.

## `URI_WEB`
Self-descriptive web URI type. Accepts a string that matches the regexp `^https?:\/\/(?:[\w\-\_]+\.)(?:\.?[\w]{2,})+([\?\/].*)?$` in Python.

## `DATETIME`
Self-descriptive datetime type. Accepts Python `datetime` ISO format value which matches the regexp `^[0-9]{4}-[0-9]{2}-[0-9]{2}T[0-9]{2}:[0-9]{2}:[0-9]{2}(\.[0-9]{6})?$`.

## `DATE`
Self-descriptive date type. Accepts Python `date` ISO format value `^[0-9]{4}-[0-9]{2}-[0-9]{2}$`.

## `TIME`
Self-descriptive time type. Accepts Python `time` ISO format value which matches the regexp `^[0-9]{2}:[0-9]{2}(:[0-9]{2}(\.[0-9]{6})?)?$`.

## `FILE`
Self-descriptive file type. Accepts a Python `dict` that has the file attributes as required by Nawah which are: `name`, `size`, `type`, `lastModified` and `content`.

### `FILE`:types
Option `types` of type `FILE` allows developers to specify list of accepted file MIME types for the attr in form of list of accepted files MIME types. e.g. `['image/*', 'video/*']` will allow all files which are `image` and `video`, or `['image/ong']` will allow only JPEG images.

## `GEO`
A GeoJSON-compatible `Point` type in the form of Python `dict` with  attributes `type` set always to `'Point'`, `coordinates` a list of integers/floats representing longitude, latitude in sequence. 

## `LIST`
Type `LIST` is used to define list of other types. e.g. `Blog` module, can have the `tag` attr set to `ATTR.LIST(list=[ATTR.STR()])` to flag that it accepts list of strings. You can pass more than one type in the list to allow multiple types.

### `LIST`:list
Option `list` of type `LIST` contains all allowed types in the attr list. If only one type is passed, only that type would strictly be checked.

### `LIST`:min
Option `min` of type `LIST` specifies minimum number of values required in the attr list. If not specified, empty lists would be accepted as valid value to the attr.

### `LIST`:max
Option `max` of type `LIST` specifies maximum number of values in the attr list.

## `DICT`
Type `DICT` matches native Python `dict` type. Type `DICT` requires option `dict` which specifies the structure of the accepted `dict` for the attr.

### `DICT`:dict
Option `dict` of type `DICT` can be be used to specify the accepted `dict` for the attr in two methods:

#### key-value dict
Developers can use this method to define `DICT` that accepts any Python `dict` with specific `__key`, `__val` types. e.g. `DICT` attr can be set to
```python
ATTR.DICT(dict={
	'__key':ATTR.STR(),
	'__val':ATTR.INT()
})
```
to accept any `dict` with its keys as strings, and values as integers. Further more, developers can specify `__min`, and `__max` to specify minimum and maximum number of attributes in `dict`. Also, developers can specify `__req`, a list of strings specifying required keys, which can help developers create type checks for `DICT` attrs with required and optional keys.

> Note JSON objects which are the format of MongoDB docs; The single unit of data in the database, does only accept strings as keys to objects, thus, `DICT` attrs with type other than `STR`, `LITERAL` will raise `InvalidAttrTypeArgException` at time of Nawah app launch.

#### typed-dict
Developers can use this method to define `DICT` the accepts Python `dict` with specific attributes, and respective types. e.g. `DICT` attr can be set to:
```python
ATTR.DICT(dict={
	'event':ATTR.STR(),
	'trigger':ATTR.ID(),
	'notes':ATTR.LOCALE()
})
```
to accept only `dict` that has the keys `event`, `trigger`, and `notes` along the correct type for every attribute.

## `LITERAL`
Type `LITERAL` is used to define set of options for the attr. Options are passed as option `literal`, a list of strings, integers, floats, or booleans. Any other value not from `literal` option list would be considered wrong value. e.g. `Shipment` module with attr `status` can have the type `LITERAL` as `ATTR.LITERAL(literal=['at-warehouse', 'shipped', 'received', 'cancelled'])` to only accept either of the values supplied in `literal` option.

### `LITERAL`:literal
Option `literal` of type `LITERAL` contains all allowed values for the attr. Values can be strings, integers, floats, or booleans.

## `UNION`
Type `UNION` is used to define an attr accepting multiple types. The attr value would be checked against all the supplied types and accept at first matching type. e.g. `Post` module with attr `attachment` can have the type `UNION` as:
```python
ATTR.UNION(union=[
	ATTR.FILE(types=['image/*', 'video/*']),
	ATTR.GEO()
])
```
to allow it to only accept attachment of either of the types supplied in `union` option.

### `UNION`:union
Option `union` of type `UNION` contains all allowed types for the attr.

## `TYPE`
Developers can define Nawah apps-specific attrs types as part of (Nawah packages)[/api/package.md] (configs)[/api/config.md]. Attrs types should go along (`types` Config Attr)[/api/config.md#types] as Python `dict`, with the keys being the attrs types, and values being Python `callables` that returns the value as-is or converted, or raises one of the [Attrs Types Exceptions](#attrs-types-exceptions).

## Attrs Types Exceptions
The sequence of data validation and type-check in Nawah works using two functions located in `utils` Python module of Nawah. The functions are `validate_doc`, and `validate_attr`. At the event of any failure these functions would raise one of the three attrs types Exceptions:

### MissingAttrException
In cases where Nawah is matching set of data against (`Attrs`)[/api/module.md#attrs] or (GET methods `query_args`)[/api/method.md#query_args] that require all `attrs` to be defined, if any is missing exception `MissingAttrException` would be raised. The exception has only one attribute `attr_name` which can be used to determine which `attr` is missing.

### InvalidAttrException
In case where Nawah find a type mismatch, exception `InvalidAttrException` would be raised. The exception has three attributes `attr_name`, `attr_type`, `val_type` which respectively refer to `attr` name, expected type, and actual value of the `attr`.

### ConvertAttrException
In few cases, Nawah would accept a type as another and converts it. This was introduced to allow developers from easily passing data types that might be not possible to via (requests)[/api/request.md], mainly for binary data. e.g. this allows Nawah to validate `attr` of (type `ID`)[#id] as `str` value and converts it to `ObjectId`. If the passed value is of the acceptable type, i.e. `str`, but failed to convert to actual type, i.e. `ID`, exception `ConvertAttrException` would be raised. The exception has three attributes `attr_name`, `attr_type`, `val_type` which respectively refer to `attr` name, expected type, and actual value of the `attr`.
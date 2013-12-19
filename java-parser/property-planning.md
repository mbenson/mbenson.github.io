##Introduction##
A Java bean property is defined by the methods which follow the related naming convention:

###Accessor###
*PropertyType* `get`*PropertyName*`();`

###Mutator###
`void set`*PropertyName*`(`*PropertyType*`);`

The presence of either such method defines a property. Commonly a property will be implemented by having the accessor access, and/or the modifier modify, a field:

`private `*PropertyType propertyName*`;`

It is customary to handle the property via the accessor and/or mutator, therefore there is most often no reason for the field to have e.g. `protected` access.


The goal of this document is to describe the modeling of Property in terms of the `java-parser` APIs using a number of interfaces:

###Property&lt;O&gt;###
Describes a readable Java bean property:

 * `Type<O> getType();`
 * `String getName();`
 * `boolean isReadable();`
 * `boolean isWritable();`
 * `Method<O> getAccessor();`
 * `Method<O> getMutator();`
 * `boolean hasField();`
 * `Field<O> getField();`

###PropertyHolder&lt;O&gt;###
Describes a Java type which may expose one or more Java bean properties:

* `boolean hasProperty(String);`
* `boolean hasProperty(Property);`
* `Property<O> getProperty(String);`
* `List<? extends Property<O>> getProperties();`

###PropertySource&lt;O&gt; extends Property&lt;O&gt;###
Describes a writable Java bean property:

 * `PropertySource<O> setType(Class<?> clazz);`
 * `PropertySource<O> setType(String type);`
 * `PropertySource<O> setType(JavaType<?> entity);`
 * `MethodSource<O> createAccessor();`
 * `PropertySource<O> removeAccessor();`
 * `MethodSource<O> createMutator();`
 * `PropertySource<O> removeMutator();`
 * `FieldSource<O> createField();`
 * `PropertySource<O> removeField();`

###PropertyHolderSource&lt;O&gt; extends PropertyHolder&lt;O&gt;###
Describes a Java source type which permits the creation and removal of its properties:

 * `PropertySource<O> addProperty();`
 * `PropertySource<O> addProperty(String type, String name);`
 * `O removeProperty(Property<O> property);`
 * `@Override List<PropertySource<O>> getProperties();`
 * `@Override PropertySource<O> getProperty(String name);`

##Implementation considerations##

 * `JavaClass<0> implements PropertyHolder<O>`
 * `JavaInterface<O> implements PropertyHolder<O>`
 * Should `JavaEnum<O>` implement `PropertyHolder<O>`?  Despite the fact that mutator methods are ill-advised, it is possible and occasionally quite convenient for these to expose Java bean properties.
 * `JavaClassSource<O> implements PropertyHolderSource<O>` (normal field/accessor/mutator when creating new)
 * `JavaInterfaceSource<O> implements PropertyHolderSource<O>` (abstract methods, no fields)
 * *if* `JavaEnumSource<O> implements PropertyHolderSource<O>`, omit setters when creating new; consider making fields `final` (would create compilation errors which would have to be addressed by the java-parser consumer)
 * `PropertyHolderSource` implementations should throw `IllegalStateException` when requested to create already-existing properties
 * `PropertySource` implementations should throw `IllegalStateException` when requested to create already-existing members
 * Should the `Field` concern be broken into separate sub-interfaces?  Feels a little heavy but perhaps worth considering.
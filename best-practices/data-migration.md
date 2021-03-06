<frontmatter>
  pageNav: 2
  pageNavTitle: "Chapters of This Page"
</frontmatter>

# Data Migration Best Practices

Data migration is necessary when a new, possibly backwards-incompatible, schema
is introduced in the code base.

- [Data Migration Best Practices](#data-migration-best-practices)
  - [Making code work for both schemas](#making-code-work-for-both-schemas)
  - [Interchangeability between consecutive versions](#interchangeability-between-consecutive-versions)

## Making code work for both schemas

Initially, the breaking change should be written such that it works for both the
old data schema and the new data schema. This can be done in two ways:

- Using
  [Objectify lifecycle annotation `@OnLoad`](https://github.com/objectify/objectify/wiki/LifecycleCallbacks)

  - In this lifecycle call, one could convert data in the old schema to the new
    schema.
  - However, it would be difficult to write client script to migrate the old
    schema to the new schema as all entities loaded by Objectify in the client
    script will be in new data schema because of the lifecycle call. Therefore,
    it is hard to differentiate the data in old schema and the data in new
    schema.

- Using Getter and Setter
  - This is the **recommended** way to convert data from the old schema to the
    new schema. It allows you to storage data in the new schema without knowing
    the underlying data schema.
  - In doing this, data migration script can use Java Reflection to determine
    whether it is old data or not and convert it accordingly.

## Interchangeability between consecutive versions

It is undeniable that the version using old data schema should be
interchangeable with the immediate next version using the new data schema. This
is why it is important to write code that works for both schemas.

In addition, it is also important to make sure the code work for both the
version tailored for the old data schema and the **immediate** next version
without hacks of the old schema.

For example, a field `isInNewDataSchema` is introduced to take care of the old
schema. It will be imprudent to remove the field in the immediate next version
after the client script is run as all data generated by the new version will
have this field missing and be _wrongly_ treated as data in old schema in the
old version. In the immediate next version, the field should be marked to `true`
although it has no usage. In the version after the immediate next version, the
field can be safely removed. That is, we should make any two consecutive
versions interchangeable at **ALL** times.

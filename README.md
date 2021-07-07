# Capacitor-sqlite-test app

This repository was made to demonstrate the bug in Android JSArray 
with a set of Null values

if `values` is defined in typescript as [10, null, null, null, null]

the Android Code

```java
JSArray values = call.getArray("values");
```
will return values [10, null,"...","...","..."]

which is wrong.

You will see the bug in cloning the app, building it and following the standard capacitor process to sync, copy, open android

A temporary fix has been implemented in `@capacitor-community/sqlite@3.0.0` definition.ts file to replace `null` with `undefined` which 

```ts
  async run(
    statement: string,
    values?: any[],
    transaction = true,
  ): Promise<capSQLiteChanges> {
    let res: any;
    try {
      if (values && values.length > 0) {
        console.log(`$$$$ in dbConnection ${values.length}`);
        console.log(`$$$$ in dbConnection values ${values}`);
        /* temporary fix for set of null values  below */
        const vals: any[] = [];
        for (const val of values) {
          if (val != null) {
            vals.push(val);
          } else {
            vals.push(undefined);
          }
        }
        console.log(`$$$$ in dbConnection vals ${vals}`);

        res = await this.sqlite.run({
          database: this.dbName,
          statement: statement,
          values: vals,
          transaction: transaction,
        });
        /* end of temporary fix for set of null values*/

        /* original code 
        res = await this.sqlite.run({
          database: this.dbName,
          statement: statement,
          values: values,
          transaction: transaction,
        });
        */
      } else {
        res = await this.sqlite.run({
          database: this.dbName,
          statement: statement,
          values: [],
          transaction: transaction,
        });
      }
      return Promise.resolve(res);
    } catch (err) {
      return Promise.reject(err);
    }
  }
```
You can replace `@capacitor-community/sqlite@3.0.0-rc.2` by `@capacitor-community/sqlite@3.0.0` ,In that case it will work as we use the temporary fix.
Hope this will be enough to clarify the issue and solve it

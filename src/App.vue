<template>
  <ion-app>
    <ion-router-outlet />
  </ion-app>
</template>

<script lang="ts">
import { IonApp, IonRouterOutlet } from '@ionic/vue';
import { defineComponent } from 'vue';
import { CapacitorSQLite, SQLiteConnection } from '@capacitor-community/sqlite';

export default defineComponent({
  name: 'App',
  components: {
    IonApp,
    IonRouterOutlet
  },

  async created() {
    const schema = `CREATE TABLE finance (
      id INTEGER PRIMARY KEY NOT NULL,
      amount REAL NOT NULL,
      description TEXT,
      wallet_id INTEGER,
      units INTEGER,
      units_amount REAL,
      FOREIGN KEY (wallet_id) REFERENCES wallet(id)
    );

    CREATE TABLE wallet (
      id INTEGER PRIMARY KEY NOT NULL,
      name TEXT NOT NULL
    );`;

    const sqlite = new SQLiteConnection(CapacitorSQLite);

    const isDatabase = await sqlite.isDatabase('test');

    const db = await sqlite.createConnection('test', false, 'no-encryption', 1);

    await db.open();

    if (!isDatabase.result) {
      await db.execute(schema);
    }

    try {
      const ret = await db.run(
        'INSERT INTO finance (amount, description, wallet_id, units, units_amount) VALUES (?,?,?,?,?)',
        [10, null, null, null, null]
      );
      console.log(`ret 1 ${JSON.stringify(ret)}`)
    } catch(err) {
      console.log(`err 1 ${err}`) 
      err
    }

    try {
      const ret = await db.run(
        'INSERT INTO finance (amount, description, wallet_id, units, units_amount) VALUES (?, ?, ?, ?, ?)',
        [-10, undefined, undefined, undefined, undefined]
      );
      console.log(`ret 2 ${JSON.stringify(ret)}`)
    } catch(err) {
      console.log(`err 2 ${err}`) 
      err
    }
    try {
      const ret = await db.run(
        'INSERT INTO wallet (id,name) VALUES (?, ?)',
        [1, "myTest"]
      );
      console.log(`ret 3 ${JSON.stringify(ret)}`)
    } catch(err) {
      console.log(`err 3 ${err}`) 
      err
    }

    try {
      const ret = await db.run(
        'INSERT INTO finance (amount, description, wallet_id, units, units_amount) VALUES (?, ?, ?, ?, ?)',
        [20, 'test', 1, 1, 20]
      );
      console.log(`ret 4 ${JSON.stringify(ret)}`)
    } catch(err) {
      console.log(`err 4 ${err}`) 
      err
    }

    const result = await db.query('SELECT * FROM finance');

    console.log(result);
  }
});
</script>
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
      await db.run(
        'INSERT INTO finance (amount, description, wallet_id, units, units_amount) VALUES (?, ?, ?, ?, ?)',
        [10, null, null, null, null]
      );
    } catch(err) { err }

    try {
      await db.run(
        'INSERT INTO finance (amount, description, wallet_id, units, units_amount) VALUES (?, ?, ?, ?, ?)',
        [-10, undefined, undefined, undefined, undefined]
      );
    } catch(err) { err }

    try {
      await db.run(
        'INSERT INTO finance (amount, description, wallet_id, units, units_amount) VALUES (?, ?, ?, ?, ?)',
        [20, 'test', 1, 1, 20]
      );
    } catch(err) { err }

    const result = await db.query('SELECT * FROM finance');

    console.log(result);
  }
});
</script>
## Basic usage of IndexDB in JavaScript
Example to save and load a string (id)


```zsh
DB_NAME = "my-db";
DB_VERSION = 1;

async function save(id:string) {
    const request = indexedDB.open(DB_NAME, DB_VERSION);
    request.onupgradeneeded = function (event: any) {
    const db = event?.target?.result;
    if (!db) return;
    const objectStore = db.createObjectStore(DB_NAME, {
        keyPath: "id",
    });
    objectStore.transaction.oncomplete = () => {
        const objectStore = db
        .transaction(DB_NAME, "readwrite")
        .objectStore(DB_NAME);
        objectStore.add({ id });
    };
    };
}

async function load() {
    const request = indexedDB.open(DB_NAME, DB_VERSION);
    request.onsuccess = (event: any) => {
    const db = event?.target?.result;
    db
        .transaction(DB_NAME)
        .objectStore(DB_NAME)
        .getAll().onsuccess = function (event: any) {
            setId(event?.target?.result[0]?.id ?? null);
    }   ;
    };
}

function setId(id: string | null){
    //...
}
```

## Ejemplo Patrón Adaptador

Ejemplo del patrón Adapter para hacer fetch de datos usando Fetch API y Axios en un proyecto Node.js con TypeScript:

**Árbol de directorios:**

```
src/
├── plugins/
│   ├── fetch-adapter.ts
│   └── axios-adapter.ts
├── services/
│   └── data-service.ts
├── interfaces/
│   └── http-client.ts
└── index.ts
```

**Nota:** El directorio plugins tambien se suele llamar **adapters**

**Código:**

**1. `src/interfaces/http-client.ts`:**

```typescript
// src/interfaces/http-client.ts
export interface HttpClient {
  get<T>(url: string): Promise<T>;
}
```

**2. `src/plugins/fetch-adapter.ts`:**

```typescript
// src/plugins/fetch-adapter.ts
import { HttpClient } from '../interfaces/http-client';

export class FetchAdapter implements HttpClient {
  async get<T>(url: string): Promise<T> {
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    return response.json() as Promise<T>;
  }
}
```

**3. `src/plugins/axios-adapter.ts`:**

```typescript
// src/plugins/axios-adapter.ts
import axios from 'axios';
import { HttpClient } from '../interfaces/http-client';

export class AxiosAdapter implements HttpClient {
  async get<T>(url: string): Promise<T> {
    const response = await axios.get<T>(url);
    return response.data;
  }
}
```

**4. `src/services/data-service.ts`:**

```typescript
// src/services/data-service.ts
import { HttpClient } from '../interfaces/http-client';

export class DataService {
  constructor(private httpClient: HttpClient) {}

  async fetchData<T>(url: string): Promise<T> {
    return this.httpClient.get<T>(url);
  }
}
```

**5. `src/index.ts`:**

```typescript
// src/index.ts
import { FetchAdapter } from './plugins/fetch-adapter';
import { AxiosAdapter } from './plugins/axios-adapter';
import { DataService } from './services/data-service';

async function main() {
  const url = 'https://jsonplaceholder.typicode.com/todos/1';

  // Usando FetchAdapter
  const fetchAdapter = new FetchAdapter();
  const fetchService = new DataService(fetchAdapter);
  const fetchDataWithFetch = await fetchService.fetchData<{ title: string }>(url);
  console.log('Data fetched with Fetch:', fetchDataWithFetch);

  // Usando AxiosAdapter
  const axiosAdapter = new AxiosAdapter();
  const axiosService = new DataService(axiosAdapter);
  const fetchDataWithAxios = await axiosService.fetchData<{ title: string }>(url);
  console.log('Data fetched with Axios:', fetchDataWithAxios);
}

main();
```

**Explicación:**

* **`interfaces/http-client.ts`:** Define la interfaz `HttpClient` con un método `get`, que es la interfaz común que nuestros adaptadores implementarán.
* **`plugins/fetch-adapter.ts` y `plugins/axios-adapter.ts`:** Estos son los adaptadores. Cada uno implementa la interfaz `HttpClient` pero utiliza una librería diferente para hacer la petición HTTP (Fetch API y Axios, respectivamente).
* **`services/data-service.ts`:** Este servicio utiliza la interfaz `HttpClient` para hacer la petición de datos. No le importa qué implementación de `HttpClient` se está utilizando, lo que permite intercambiar fácilmente entre Fetch API y Axios.
* **`index.ts`:** Este es el punto de entrada de la aplicación. Aquí, creamos instancias de `FetchAdapter` y `AxiosAdapter`, las pasamos a `DataService`, y luego llamamos a `fetchData`.

**Para ejecutar el código:**

1.  Asegúrate de tener Node.js y npm instalados.
2.  Crea un nuevo proyecto Node.js y configura TypeScript.
3.  Instala las dependencias: `npm install axios @types/axios node-fetch @types/node-fetch`
4.  Compila el código TypeScript: `tsc`
5.  Ejecuta el código JavaScript generado: `node dist/index.js` (o el nombre que hayas configurado en tu `tsconfig.json`).


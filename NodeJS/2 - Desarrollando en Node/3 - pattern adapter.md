## Patrón Adaptador en Node.js y TypeScript con Arquitectura Limpia

El **Patrón Adaptador** es un patrón estructural que permite que dos interfaces incompatibles trabajen juntas sin modificar su código original. En una arquitectura limpia, este patrón se usa comúnmente para desacoplar la lógica de negocio de tecnologías externas como bases de datos, APIs de terceros, o sistemas de almacenamiento.

---

## **📌 ¿Por qué usar el Patrón Adaptador en Arquitectura Limpia?**  
En una **arquitectura limpia**, la capa de aplicación (casos de uso) no debería depender directamente de detalles de infraestructura como APIs externas o bases de datos. Aquí es donde entra el **Adaptador**, que actúa como una "capa intermedia" entre el **caso de uso** y la **infraestructura**.

✅ **Beneficios**:  
✔️ Desacopla la lógica de negocio de la infraestructura.  
✔️ Permite cambiar APIs externas sin afectar la aplicación.  
✔️ Facilita la prueba unitaria, ya que puedes inyectar adaptadores falsos (mocks).  

---

## **📂 Estructura de Carpetas en una Arquitectura Limpia con Adaptador**
```
/src
 ├── application (Lógica de negocio / Casos de uso)
 │    ├── use-cases
 │    │    ├── GetWeatherUseCase.ts
 │    ├── interfaces
 │         ├── IWeatherService.ts
 │    
 ├── infrastructure (Implementaciones externas)
 │    ├── adapters
 │    │    ├── OpenWeatherAdapter.ts
 │    ├── services
 │         ├── ExternalWeatherAPI.ts
 │    
 ├── presentation (Capa de controladores / API)
 │    ├── controllers
 │    │    ├── WeatherController.ts
 │    
 ├── index.ts (Punto de entrada)
```

---

## **🛠 Ejemplo: Implementación del Patrón Adaptador**  
Vamos a suponer que nuestra aplicación necesita obtener información del clima desde una API externa, como **OpenWeatherMap**. Sin embargo, no queremos que nuestra lógica de negocio dependa directamente de esta API, así que usaremos un **Adaptador**.

---

### **1️⃣ Definir la Interfaz Esperada (`IWeatherService.ts`)**  
Creamos una interfaz en la capa de **aplicación**, definiendo el contrato que debe cumplir cualquier servicio de clima.

```ts
// src/application/interfaces/IWeatherService.ts
export interface IWeatherService {
  getTemperature(city: string): Promise<number>;
}
```
📌 **Objetivo:** Cualquier servicio que implemente esta interfaz debe tener un método `getTemperature()` que devuelva la temperatura de una ciudad.

---

### **2️⃣ Implementar el Servicio Externo (`ExternalWeatherAPI.ts`)**  
Este es el servicio que se comunica con OpenWeatherMap. Podría ser cualquier API externa.

```ts
// src/infrastructure/services/ExternalWeatherAPI.ts
import axios from 'axios';

export class ExternalWeatherAPI {
  private apiKey: string;

  constructor(apiKey: string) {
    this.apiKey = apiKey;
  }

  async fetchWeather(city: string): Promise<{ temp: number; unit: string }> {
    const response = await axios.get(`https://api.openweathermap.org/data/2.5/weather`, {
      params: { q: city, appid: this.apiKey, units: "metric" }
    });

    return { temp: response.data.main.temp, unit: "C" };
  }
}
```
📌 **Problema:** Esta API devuelve los datos en un formato que no es el esperado por nuestra aplicación. Aquí es donde entra el **Adaptador**.

---

### **3️⃣ Crear el Adaptador (`OpenWeatherAdapter.ts`)**  
El **Adaptador** actúa como una "capa intermedia" para convertir la interfaz de `ExternalWeatherAPI` a `IWeatherService`.

```ts
// src/infrastructure/adapters/OpenWeatherAdapter.ts
import { IWeatherService } from "../../application/interfaces/IWeatherService";
import { ExternalWeatherAPI } from "../services/ExternalWeatherAPI";

export class OpenWeatherAdapter implements IWeatherService {
  private api: ExternalWeatherAPI;

  constructor(api: ExternalWeatherAPI) {
    this.api = api;
  }

  async getTemperature(city: string): Promise<number> {
    const data = await this.api.fetchWeather(city);
    return data.temp; // Devuelve la temperatura en Celsius
  }
}
```
✅ **Ventaja:** Si cambiamos de API (por ejemplo, usamos AccuWeather en lugar de OpenWeather), solo necesitamos modificar el adaptador, no la lógica de negocio.

---

### **4️⃣ Caso de Uso (`GetWeatherUseCase.ts`)**  
El caso de uso representa la lógica de negocio pura, sin depender de la infraestructura.

```ts
// src/application/use-cases/GetWeatherUseCase.ts
import { IWeatherService } from "../interfaces/IWeatherService";

export class GetWeatherUseCase {
  private weatherService: IWeatherService;

  constructor(weatherService: IWeatherService) {
    this.weatherService = weatherService;
  }

  async execute(city: string): Promise<string> {
    const temp = await this.weatherService.getTemperature(city);
    return `La temperatura en ${city} es de ${temp.toFixed(1)}°C.`;
  }
}
```
✅ **Ventaja:** Este caso de uso no sabe si los datos vienen de OpenWeather o de otro servicio.

---

### **5️⃣ Controlador (`WeatherController.ts`)**
Aquí usamos el caso de uso para exponer la funcionalidad en una API.

```ts
// src/presentation/controllers/WeatherController.ts
import { Request, Response } from "express";
import { GetWeatherUseCase } from "../../application/use-cases/GetWeatherUseCase";

export class WeatherController {
  private getWeatherUseCase: GetWeatherUseCase;

  constructor(getWeatherUseCase: GetWeatherUseCase) {
    this.getWeatherUseCase = getWeatherUseCase;
  }

  async getWeather(req: Request, res: Response): Promise<Response> {
    try {
      const city = req.params.city;
      const result = await this.getWeatherUseCase.execute(city);
      return res.json({ message: result });
    } catch (error) {
      return res.status(500).json({ error: "Error obteniendo el clima" });
    }
  }
}
```

---

### **6️⃣ Integrar Todo en `index.ts`**
Aquí conectamos las piezas en una API con Express.

```ts
// src/index.ts
import express from "express";
import { ExternalWeatherAPI } from "./infrastructure/services/ExternalWeatherAPI";
import { OpenWeatherAdapter } from "./infrastructure/adapters/OpenWeatherAdapter";
import { GetWeatherUseCase } from "./application/use-cases/GetWeatherUseCase";
import { WeatherController } from "./presentation/controllers/WeatherController";

const app = express();
const PORT = 3000;

// Inyección de dependencias
const apiKey = "TU_API_KEY";
const externalAPI = new ExternalWeatherAPI(apiKey);
const weatherAdapter = new OpenWeatherAdapter(externalAPI);
const getWeatherUseCase = new GetWeatherUseCase(weatherAdapter);
const weatherController = new WeatherController(getWeatherUseCase);

// Endpoint
app.get("/weather/:city", (req, res) => weatherController.getWeather(req, res));

app.listen(PORT, () => console.log(`Servidor corriendo en http://localhost:${PORT}`));
```

---

## **🚀 Conclusión**
🔹 **El Adaptador** permite que nuestra aplicación use la API externa sin depender directamente de ella.  
🔹 **La Lógica de Negocio (Casos de Uso)** está separada de la API externa.  
🔹 **Si cambiamos de servicio meteorológico, solo modificamos el Adaptador**, no toda la aplicación.  

✅ **Arquitectura limpia + Patrón Adaptador** = Código flexible, mantenible y fácil de testear.  

---
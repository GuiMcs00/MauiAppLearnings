## 📌 **Geoindexação no MongoDB para uso em um MAUI App**

### 🔍 **1. Nome do índice geoespacial**

O índice geoespacial mais comum é o:

```bash
2dsphere
```

Esse índice permite armazenar e consultar dados que representam pontos, linhas e polígonos na superfície da Terra.

---

### ⚙️ **2. Como criar o índice geoespacial**

Você cria um índice `2dsphere` com o seguinte comando:

```javascript
db.localizacoes.createIndex({ localizacao: "2dsphere" });
```

Aqui, `localizacao` é o campo do documento que contém as coordenadas geográficas.

---

### 🧩 **3. Estrutura esperada dos dados**

```json
{
  "usuarioId": "abc123",
  "localizacao": {
    "type": "Point",
    "coordinates": [-47.9292, -15.7801] // [longitude, latitude]
  },
  "dataAtualizacao": "2025-05-22T12:00:00Z"
}
```

---

### 📡 **4. Consulta por proximidade**

Você pode buscar documentos próximos a um ponto com:

```javascript
db.localizacoes.find({
  localizacao: {
    $near: {
      $geometry: {
        type: "Point",
        coordinates: [-47.9292, -15.7801],
      },
      $maxDistance: 5000, // em metros
    },
  },
});
```

---

### 🔗 **5. Documentação oficial do MongoDB**

- [MongoDB Geospatial Indexes](https://www.mongodb.com/docs/manual/core/geospatial-indexes/)
- [MongoDB \$near Operator](https://www.mongodb.com/docs/manual/reference/operator/query/near/)
- [MongoDB 2dsphere Index](https://www.mongodb.com/docs/manual/core/2dsphere/)

---

### 🧪 **6. Aplicação prática no MAUI App**

No seu app, você pode usar essas coordenadas obtidas com `Geolocation.GetLocationAsync()` para:

- enviar ao servidor e armazenar com `2dsphere`
- buscar locais ou usuários próximos com uma chamada à API que usa `$near`

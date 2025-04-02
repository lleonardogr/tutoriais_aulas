# Guia Introdutório ao ML.NET

## Introdução

**ML.NET** é um framework de código aberto desenvolvido pela Microsoft que permite aos desenvolvedores .NET criar modelos de aprendizado de máquina (Machine Learning) personalizados usando C# ou F#. Com o ML.NET, é possível incorporar recursos de IA em suas aplicações sem a necessidade de experiência prévia em aprendizado de máquina.

---

## 1️⃣ Configurando o Ambiente

### 🖥️ Requisitos

- **Visual Studio 2019 ou superior** (qualquer edição) ou **Visual Studio Code**
- **.NET 6.0 SDK** ou superior
- **ML.NET CLI** (opcional, para criação de modelos automatizados)

### 📦 Instalando o ML.NET

#### Via **Package Manager Console**:

```bash
Install-Package Microsoft.ML
```

#### Via **.NET CLI**:

```bash
dotnet add package Microsoft.ML
```

---

## 2️⃣ Conceitos Básicos do ML.NET

### 🔹 Pipelines de Aprendizado de Máquina

Um **pipeline** é uma sequência de transformações de dados e algoritmos que processam os dados para produzir um modelo treinado.

### 🔹 Transformações de Dados

- Normalização de valores numéricos
- Codificação de variáveis categóricas
- Extração de recursos de texto

### 🔹 Treinamento e Avaliação de Modelos

Após preparar os dados, o próximo passo é **treinar** o modelo usando um algoritmo adequado e, em seguida, **avaliá-lo**.

---

## 3️⃣ Exemplo Prático: Regressão Linear

### 🎯 Objetivo

Vamos criar um modelo que prevê o preço de casas com base em características como metragem quadrada e número de quartos.

### 🔹 Criando o Projeto

```bash
dotnet new console -n RegressaoMLNet
cd RegressaoMLNet
dotnet add package Microsoft.ML
```

---

### 🔹 Definindo as Classes de Dados

```C#
using Microsoft.ML.Data;

public class HouseData
{
    [LoadColumn(0)]
    public float Tamanho { get; set; }

    [LoadColumn(1), ColumnName("Label")]
    public float Preco { get; set; }
}

public class HousePricePrediction
{
    [ColumnName("Score")]
    public float PrecoPrevisto { get; set; }
}
```

---

### 🔹 Construindo o Pipeline

```C#
var mlContext = new MLContext();
var dataView = mlContext.Data.LoadFromTextFile<HouseData>("dados.csv", hasHeader: true, separatorChar: ',');

var pipeline = mlContext.Transforms.Concatenate("Features", "Tamanho")
    .Append(mlContext.Regression.Trainers.LbfgsPoissonRegression());

var model = pipeline.Fit(dataView);
```

---

### 🔹 Fazendo Previsões

```C#
var predictionFunction = mlContext.Model.CreatePredictionEngine<HouseData, HousePricePrediction>(model);
var novaCasa = new HouseData { Tamanho = 85f };
var precoPrevisto = predictionFunction.Predict(novaCasa);

Console.WriteLine($"Preço previsto: {precoPrevisto.PrecoPrevisto:#.##}");
```

---

## 📚 Recursos Adicionais

- [Documentação Oficial do ML.NET](https://docs.microsoft.com/pt-br/dotnet/machine-learning/)
- [Exemplos no GitHub](https://github.com/dotnet/machinelearning-samples)

---
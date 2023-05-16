# Taller 12

### Versionamiento de modelos - MLflow

##### Analítica Computacional para la Toma de Decisiones

---

|     Nombres      |      Apellidos       |     Login     |  Codigo   |
| :--------------: | :------------------: | :-----------: | :-------: |
|     Santiago     | Gonzalez Montealegre | s.gonzalez35  | 202012274 |
| Juliana Carolina |  Cardenas Barragan   | jc.cardenasb1 | 202011683 |

---

---

## Pre-requisitos

---

1. **Nota:** defina un nombre para su grupo, defínalo claramente en reporte y use este nombre como parte inicial de todos los recursos que cree.

2. **Nota 2:** la entrega de este taller consiste en un reporte y unos archivos de soporte. Cree el archivo de su reporte como un documento de texto en el que pueda fácilmente incorporar capturas de pantalla, textos y similares. Puede ser un archivo de word, libre office, markdown, entre otros.

---

---

## Instale y pruebe MLflow localmente

---

### 1.

En su ambiente local active un ambiente de python. Por ejemplo, usando Anaconda en consola puede activar el ambiente base con el comando

```bash
activate base
```

---

### 2.

En este ambiente instale mlflow

```bash
conda install -c conda-forge mlflow
```

---

### 3.

En este ambiente instale sklearn

```bash
conda install -c conda-forge scikit-learn
```

---

### 4.

En una terminal aparte lance la interfaz gráfica de MLflow con el comando

```bash
mlflow ui
```

Tome un pantallazo de la salida de la terminal e inclúyalo en su reporte.

![Salida Termina](/Image/1.4.png)

---

### 5.

La interfaz web debe quedar disponible a través del navegador en el puerto 5000. Abra la ubicación http://localhost:5000 y compruebe que funciona.

---

### 6.

Ahora ejecute el notebook mlflow-diab que encontrará en Bloque Neón. Estudie con cuidado el código y describa en su reporte qué hace.

```py
# Importe el conjunto de datos de diabetes y divídalo en entrenamiento y prueba usando scikit-learn
from sklearn.model_selection import train_test_split
from sklearn.datasets import load_diabetes

db = load_diabetes()
X = db.data
y = db.target
X_train, X_test, y_train, y_test = train_test_split(X, y)
```

En la primera parte del código se importa el conjunto de datos de diabetes y se divide en conjuntos de entrenamiento y prueba utilizando la biblioteca `scikit-learn`.

Luego asigna las características (X) y la variable de interes (y) de la base de datos y realiza la división utilizando la función `train_test_split()`.

Los conjuntos resultantes son `X_train, X_test, y_train, ` y `y_test`, que se utilizarán para entrenar y evaluar modelos de aprendizaje automático.

```py
#Importe MLFlow para registrar los experimentos, el regresor de bosques aleatorios y la métrica de error cuadrático medio
import mlflow
import mlflow.sklearn
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import mean_squared_error

# defina el servidor para llevar el registro de modelos y artefactos
mlflow.set_tracking_uri('http://localhost:5000')
# registre el experimento
experiment = mlflow.set_experiment("sklearn-diab")

# Aquí se ejecuta MLflow sin especificar un nombre o id del experimento. MLflow los crea un experimento para este cuaderno por defecto y guarda las características del experimento y las métricas definidas.
# Para ver el resultado de las corridas haga click en Experimentos en el menú izquierdo.
with mlflow.start_run(experiment_id=experiment.experiment_id):
    # defina los parámetros del modelo
    n_estimators = 200
    max_depth = 6
    max_features = 4
    # Cree el modelo con los parámetros definidos y entrénelo
    rf = RandomForestRegressor(n_estimators = n_estimators, max_depth = max_depth, max_features = max_features)
    rf.fit(X_train, y_train)
    # Realice predicciones de prueba
    predictions = rf.predict(X_test)

    # Registre los parámetros
    mlflow.log_param("num_trees", n_estimators)
    mlflow.log_param("maxdepth", max_depth)
    mlflow.log_param("max_feat", max_features)

    # Registre el modelo
    mlflow.sklearn.log_model(rf, "random-forest-model")

    # Cree y registre la métrica de interés
    mse = mean_squared_error(y_test, predictions)
    mlflow.log_metric("mse", mse)
    print(mse)
```

En la segunda parte del código se comienza importando las bibliotecas MLflow para registrar experimentos y métricas, el regresor de bosques aleatorios y la métrica de error cuadrático medio.

Luego se establece la URI de seguimiento de MLflow para especificar el servidor donde se registrarán los modelos y los artefactos y se registra un nuevo experimento llamado `"sklearn-diab"` en MLflow.

Se inicia una nueva ejecución de MLflow dentro del experimento especificado. Se definen los parámetros del modelo de bosques aleatorios, como el número de estimadores, la profundidad máxima y el número máximo de características, a partir de estos se crea un modelo de bosques aleatorios y se entrena utilizando los conjuntos de entrenamiento.

Se realizan las predicciones utilizando el modelo entrenado en los datos de prueba. Se registran los parámetros y el modelo de bosques aleatorios en MLflow. Se calcula el error cuadrático medio entre las predicciones y los valores reales de prueba. Registrando este valor en MLflow y imprimiendolo en la consola.

---

### 7.

Modifique alguno de los parámetros del modelo y vuelva a correr el modelo.

---

### 8.

Ahora visite la interfaz web de MLflow e identifique el experimento y las dos corridas realizadas. Compare los dos modelos en términos de MSE (mean squared error) usando las funciones de la interfaz. Incluya un pantallazo de la comparación en su reporte.

Los dos experimentos realizados se muestran a continuación:
![Experimentos](/Image/1.81.png)

Comparación MSE entre los experimentos realizados:
![Comparación](/Image/1.82.png)

**Experimento 1:**

![E1](/Image/1.83.png)
![E1](/Image/1.85.png)

**Experimento 2:**
![E2](/Image/1.84.png)
![E1](/Image/1.86.png)

---

---

## MLflow en Databricks

---

### 1.

Inicie creando una cuenta en Databricks Community Edition https://www.databricks.com/try-databricks#account

---

### 2.

Provea sus datos y un correo.

---

### 3.

En la sección **Choose a cloud provider** en la parte inferior seleccione **Get started with Community Edition.**

---

### 4.

Valide su correo y genere su contraseña.

---

### 5.

Una vez haya creado la cuenta, ingrese con sus credenciales y familiarícese con la consola principal.

---

### 6.

En el primer ítem del menú de la izquiera seleccione Data Science and Engineering.

---

### 7.

Luego de click en Create - Cluster.

---

### 8.

Asigne un nombre al cluster, seleccione un Runtime de ML, en su versión más reciente.

---

### 9.

Click en crear cluster y espere a que el cluster se haya creado. Incluya un pantallazo de su clúster operando en su reporte.

![Cluster](/Image/2.10.png)

---

### 10.

En el primer ítem del menú de la izquierda seleccione Machine Learning.

---

### 11.

En el menú de la izquierda click en Create - Notebook.

---

### 12.

Asigne un nombre al notebook, con lenguaje python por defecto y asígnelo al cluster que acaba de crear.

---

### 13.

Con el notebook abierto, copie las instrucciones del notebook mlflow-diab que encontrará en Bloque Neón.

---

### 14.

Revise y comprenda las instrucciones de cada celda.

---

### 15.

Ejecute cada celda y explore los resultados.

---

### 16.

En el menú de la izquierda seleccione Experiments.

---

### 17.

Seleccione el experimento realizado y navegue la documentación asociada (versión del modelo, librerías, python).

---

### 18.

Cambie los parámetros del entrenamiento del modelo y ejecute un nuevo experimento.

---

### 19.

Revise los resultados en Experiments, genere gráficas comparativas. Incluya un pantallazo de las gráficas en su reporte.

Comparación MSE entre experimentos:
![E1](/Image/2.201.png)

Si solo se cambia el parametro `"num_trees"` en el experimento
Grafica comparativa entre experimentos y el MSE:

![E1](/Image/2.202.png)

Si solo se cambia el parametro `"max_feat"` en el experimento
Grafica comparativa entre experimentos y el MSE:

![E1](/Image/2.203.png)

Si solo se cambia el parametro `"maxdepth"` en el experimento
Grafica comparativa entre experimentos y el MSE:

![E1](/Image/2.204.png)

---

### 20.

Repita este procedimiento varias veces.

---

### 21.

Repita este procedimiento con el notebook mlflow-mnist que encontrará en Bloque Neón. Incluya un pantallazo de las gráficas en su reporte.

Se realizan 3 experimentos donde los resultados de `"accuracy"` se observan a continuación:

![E1](/Image/2.221.png)

Se cambian los parametros de `"batch_size"` y `"epochs"`, los resultados se observan a continuación:

![E1](/Image/2.222.png)

---

### 22.

En su reporte explique en qué consiste este último notebook y los modelos que allí se entrenan. Documente alguna observación sobre sus resultados.

```py
# Importe MLflow, ketas y tensorflow
import mlflow
import mlflow.keras
import keras
import tensorflow as tf
import tensorflow.keras as tk
from keras import models
from keras import layers
```

En la primera parte del código se importan las librerias con las que se va a trabajar.

```py
# Usaremos argparse para pasarle argumentos a las funciones de entrenamiento
import argparse

parser = argparse.ArgumentParser(description='Entrenamiento de una red feed-forward para el problema de clasificación con datos MNIST en TensorFlow/Keras')
parser.add_argument('--batch_size', '-b', type=int, default=128)
parser.add_argument('--epochs', '-e', type=int, default=5)
parser.add_argument('--learning_rate', '-l', type=float, default=0.05)
parser.add_argument('--num_hidden_units', '-n', type=int, default=512)
parser.add_argument('--num_hidden_layers', '-N', type=int, default=1)
parser.add_argument('--dropout', '-d', type=float, default=0.25)
parser.add_argument('--momentum', '-m', type=float, default=0.85)
```

En la segunda parte del código se definen los argumentos que se pueden pasar al script de entrenamiento y establece los valores predeterminados para aquellos argumentos que no se proporcionen al ejecutar el script.

```py
args = parser.parse_args([])
```

Se utiliza el objeto `parser` definido previamente para analizar los argumentos de línea de comandos. En este caso, se pasa una lista vacía `[]` como argumento a `parse_args()`. Esto significa que no se están proporcionando argumentos de línea de comandos al analizador en este momento.

```py
# Usaremos esta función para definir Descenso de Gradiente Estocástico como optimizador
def get_optimizer():
    """
    :return: Keras optimizer
    """
    optimizer = keras.optimizers.SGD(learning_rate=args.learning_rate,momentum=args.momentum, nesterov=True)
    return optimizer
```

Se define una función llamada `get_optimizer()` que devuelve un optimizador de Keras para el Descenso de Gradiente Estocástico (SGD, por sus siglas en inglés) con los parámetros especificados; `learning_rate`, `momentum` y `nesterov`.

```py
# Obtenemos el dataset MNIST
mnist = tk.datasets.mnist
(x_train, y_train), (x_test, y_test) = mnist.load_data()
# normalizamos los datos para que estén entre 0 y 1
x_train, x_test = x_train / 255.0, x_test / 255.0

# Esta función define una corrida del modelo, con entrenamiento y
# registro en MLflow
def run_mlflow(run_name="MLflow CE MNIST"):
    # Iniciamos una corrida de MLflow
    mlflow.start_run(run_name=run_name)
    run = mlflow.active_run()
    # MLflow asigna un ID al experimento y a la corrida
    experimentID = run.info.experiment_id
    runID = run.info.run_uuid
    # reistro automáticos de las métricas de keras
    mlflow.keras.autolog()
    model = models.Sequential()
    #
    # La primera capa de la red transforma las imágenes de un arreglo 2d (28x28 pixels),
    # en un arreglo 1d de 28 * 28 = 784 pixels.
    model.add(layers.Flatten(input_shape=x_train[0].shape))
    # Agregamos capas ocultas a la red
    # en los argumentos: --num_hidden_layers o -N
    for n in range(0, args.num_hidden_layers):
        # agregamos una capa densa (completamente conectada) con función de activación relu
        model.add(layers.Dense(args.num_hidden_units, activation=tf.nn.relu))
        # agregamos dropout como método de regularización para aleatoriamente descartar una capa
        # si los gradientes son muy pequeños
        model.add(layers.Dropout(args.dropout))
        # capa final con 10 nodos de salida y activación softmax
        model.add(layers.Dense(10, activation=tf.nn.softmax))
        # Use Scholastic Gradient Descent (SGD) or Adadelta
        # https://keras.io/optimizers/
        optimizer = get_optimizer()

    # compilamos el modelo y definimos la función de pérdida
    # otras funciones de pérdida comunes para problemas de clasificación
    # 1. sparse_categorical_crossentropy
    # 2. binary_crossentropy
    model.compile(optimizer=optimizer,
                 loss='sparse_categorical_crossentropy',
                 metrics=['accuracy'])

    # entrenamos el modelo
    print("-" * 100)
    model.fit(x_train, y_train, epochs=args.epochs, batch_size=args.batch_size)
    # evaluamos el modelo
    test_loss, test_acc = model.evaluate(x_test, y_test, verbose=2)
    mlflow.end_run(status='FINISHED')
    return (experimentID, runID)
```

En esta parte del código se define una función para ejecutar un modelo de clasificación basado en redes neuronales para el dataset MNIST, realiza el entrenamiento del modelo y registra los resultados en MLflow.

```py
# corrida con parámetros diferentes a los por defecto
args = parser.parse_args(["--batch_size", '256', '--epochs', '8'])
(experimentID, runID) = run_mlflow()
print("MLflow Run completed with run_id {} and experiment_id {}".format(runID, experimentID))
print(tf.__version__)
print("-" * 100)
```

La parte final del código ejecuta la función `run_mlflow()` con valores de argumentos específicos diferentes a los predeterminados utilizando el objeto `parser.parse_args()`. Luego, muestra información sobre la corrida de MLflow y la versión de TensorFlow utilizada.

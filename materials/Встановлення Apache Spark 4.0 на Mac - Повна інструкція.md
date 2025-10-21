## Передумови

Apache Spark 4.0 має наступні офіційні вимоги:
- **Java 17 або 21** (рекомендується Java 17)
- **Scala 2.13**
- **Python 3.9+**

---

## Крок 1: Встановлення та налаштування Homebrew

### Перевірка наявності Homebrew
Відкрий **Terminal** і введи:

```bash
brew -v
```

**Результати:**
- Якщо з'явилась версія (наприклад `Homebrew 4.3.17`) — у тебе вже є brew
- Якщо `command not found` — переходь до встановлення

### Встановлення Homebrew
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### Додавання у PATH
Після інсталяції додай до shell профілю:

**Для Apple Silicon Mac (M1/M2/M3):**
```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

**Для Intel Mac:**
```bash
echo 'eval "$(/usr/local/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/usr/local/bin/brew shellenv)"
```

### Перевірка Homebrew
```bash
brew doctor
brew -v
```

### Оновлення Homebrew (опціонально)
```bash
brew update
brew upgrade
```

---

## Крок 2: Встановлення Apache Spark

### Варіант A: Через Homebrew (Рекомендовано)

#### 1. Встановлення Apache Spark
```bash
brew update
brew install apache-spark
```

> Homebrew автоматично встановить Spark 4.x та JDK 17 як залежність

#### 2. Створення системного посилання для JDK (опціонально)
```bash
sudo ln -sfn "$(brew --prefix)/opt/openjdk@17/libexec/openjdk.jdk" \
  /Library/Java/JavaVirtualMachines/openjdk-17.jdk
```

#### 3. Налаштування змінних середовища
Додай до `~/.zshrc`:

```bash
# Java 17
export JAVA_HOME=$(/usr/libexec/java_home -v 17)
export PATH="$JAVA_HOME/bin:$PATH"

# Spark з Homebrew
export SPARK_HOME="$(brew --prefix apache-spark)/libexec"
export PATH="$SPARK_HOME/bin:$PATH"
```

#### 4. Перезавантаження конфігурації
```bash
source ~/.zshrc
```

### Варіант B: Ручне встановлення

#### 1. Завантаження Spark
Відвідай [офіційну сторінку завантажень](https://spark.apache.org/downloads.html) та завантаж Spark 4.x (Pre-built for Apache Hadoop 3)

#### 2. Розпакування та розміщення
```bash
cd ~/Downloads
tar -xzf spark-4.0.0-bin-hadoop3.tgz
mkdir -p ~/opt
mv spark-4.0.0-bin-hadoop3 ~/opt/spark-4.0.0
ln -sfn ~/opt/spark-4.0.0 ~/opt/spark
```

#### 3. Встановлення JDK 17
```bash
brew install openjdk@17
sudo ln -sfn "$(brew --prefix)/opt/openjdk@17/libexec/openjdk.jdk" \
  /Library/Java/JavaVirtualMachines/openjdk-17.jdk
```

#### 4. Налаштування змінних середовища
```bash
echo 'export JAVA_HOME=$(/usr/libexec/java_home -v 17)' >> ~/.zshrc
echo 'export SPARK_HOME="$HOME/opt/spark"' >> ~/.zshrc
echo 'export PATH="$JAVA_HOME/bin:$SPARK_HOME/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

---

## Крок 3: Перевірка встановлення

### Перевірка версій
```bash
spark-shell --version
pyspark --version
spark-sql --version
java -version
```

---

## Крок 4: Налаштування PySpark та Jupyter

### Варіант 1: Використання вбудованого PySpark (рекомендовано)

#### Встановлення Jupyter
```bash
pip3 install --upgrade pip
pip3 install jupyterlab
```

#### Запуск PySpark з Jupyter
```bash
PYSPARK_DRIVER_PYTHON=jupyter PYSPARK_DRIVER_PYTHON_OPTS="lab" pyspark
```

### Варіант 2: Встановлення PySpark через PyPI

#### Створення віртуального середовища
```bash
python3 -m venv .venv
source .venv/bin/activate
pip install pyspark
```

#### Перевірка
```bash
python -c "import pyspark; print(pyspark.__version__)"
```

---

## Крок 5: Тестування роботи

### Scala REPL
```bash
spark-shell
```

У консолі виконай:
```scala
val df = spark.range(1, 1000000)
df.count()
```

### PySpark
```bash
pyspark
```

У консолі Python виконай:
```python
df = spark.range(1, 1_000_000)
df.count()
```

### Spark UI
Під час активної сесії відкрий у браузері: `http://localhost:4040`

---

## Крок 6: Налаштування локального кластера (опціонально)

### Запуск standalone кластера
```bash
# Старт майстра
"$SPARK_HOME/sbin/start-master.sh"

# Перегляд веб-інтерфейсу майстра
open http://localhost:8080

# Старт воркера (замінити на актуальний URL майстра)
"$SPARK_HOME/sbin/start-worker.sh" spark://localhost:7077
```

### Зупинка кластера
```bash
"$SPARK_HOME/sbin/stop-worker.sh"
"$SPARK_HOME/sbin/stop-master.sh"
```

---

## Крок 7: Конфігурація Spark (опціонально)

### Створення конфігураційних файлів
```bash
cp "$SPARK_HOME/conf/spark-env.sh.template" "$SPARK_HOME/conf/spark-env.sh"
cp "$SPARK_HOME/conf/spark-defaults.conf.template" "$SPARK_HOME/conf/spark-defaults.conf"
```

### Приклад налаштувань spark-env.sh
```bash
# conf/spark-env.sh
export SPARK_WORKER_CORES=4
export SPARK_WORKER_MEMORY=8g
export SPARK_DRIVER_MEMORY=4g
export SPARK_LOCAL_DIRS=/tmp/spark
```

### Приклад налаштувань spark-defaults.conf
```properties
# conf/spark-defaults.conf
spark.sql.shuffle.partitions=200
spark.driver.extraJavaOptions=-Duser.timezone=UTC
spark.executor.extraJavaOptions=-Duser.timezone=UTC
```

---

## Корисні команди

### Оновлення та управління (Homebrew)
```bash
# Оновлення Spark
brew upgrade apache-spark

# Деінсталяція
brew uninstall apache-spark

# Перегляд інформації про пакет
brew info apache-spark
```

### Запуск додатків
```bash
# Приклад запуску Spark додатка
spark-submit --class org.apache.spark.examples.SparkPi \
    "$SPARK_HOME/examples/jars/spark-examples_2.13-4.0.0.jar" 10
```

### Перегляд логів
```bash
# Логи кластера
tail -f "$SPARK_HOME/logs/*.out"

# Логи додатків
ls /tmp/spark-*
```

---

## Вирішення проблем

### Проблема з JAVA_HOME
```bash
# Перевірка поточного JAVA_HOME
echo $JAVA_HOME

# Встановлення через java_home
export JAVA_HOME=$(/usr/libexec/java_home -v 17)

# Перевірка доступних версій Java
/usr/libexec/java_home -V
```

### Проблема з портами
```bash
# Якщо порт 8080 зайнятий
"$SPARK_HOME/sbin/start-master.sh" --port 8081 --webui-port 8082

# Перевірка зайнятих портів
lsof -i :8080
lsof -i :4040
```

### Проблема з правами доступу
```bash
# Надання прав на виконання
chmod +x "$SPARK_HOME/bin/spark-shell"
chmod +x "$SPARK_HOME/bin/pyspark"
chmod +x "$SPARK_HOME/sbin/start-master.sh"
```

---

## Видалення Spark

### Для встановлення через Homebrew
```bash
brew uninstall apache-spark
brew uninstall openjdk@17  # якщо більше не потрібен

# Очистка конфігурації з ~/.zshrc
# Видали відповідні рядки з SPARK_HOME та JAVA_HOME
```

### Для ручного встановлення
```bash
rm -rf ~/opt/spark*
# Очисти змінні середовища з ~/.zshrc
```

---

## Корисні посилання

- [Apache Spark Downloads](https://spark.apache.org/downloads.html)
- [Homebrew Apache Spark Formula](https://formulae.brew.sh/formula/apache-spark)
- [Homebrew OpenJDK@17 Formula](https://formulae.brew.sh/formula/openjdk@17)
- [PySpark Documentation](https://spark.apache.org/docs/latest/api/python/)
- [Spark SQL Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html)

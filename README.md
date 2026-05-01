# Text Recognition and Playback

Pipeline для синтеза речи, распознавания и генерации ответов с помощью LLM.

## Обзор

Проект реализует полный цикл обработки голосового взаимодействия:

1. **Text-to-Speech (TTS)** — синтез речи из текстовых промптов с помощью **XTTS v2** (Coqui TTS)
2. **Speech-to-Text (STT)** — распознавание синтезированной речи обратно в текст с помощью **OpenAI Whisper (medium)**
3. **Оценка точности** — сравнение оригинального и распознанного текста (метрика Jaccard по словам)
4. **LLM-ответ** — отправка распознанного текста в **DeepSeek API** и получение ответа
5. **Озвучивание ответа** — синтез речи из ответа LLM через XTTS

## Структура ноутбука

| Ячейка | Назначение |
|--------|------------|
| 1 | Установка зависимостей (Coqui TTS, Whisper, PyDub, OpenAI SDK, ffmpeg) |
| 2 | Импорт библиотек |
| 3 | Список из 10 тестовых промптов на темы AI/LLM |
| 4 | Загрузка моделей: Whisper-medium + XTTS v2 (с поддержкой GPU) |
| 5 | Основные функции: `generate_audio`, `prepare_audio`, `recognize_with_whisper`, `postprocess`, `calculate_accuracy` |
| 6 | Главный пайплайн: синтез → распознавание → оценка точности |
| 7 | Интеграция с DeepSeek API: запрос → ответ → озвучивание |

## Зависимости

- **Python 3.x**
- **PyTorch** + CUDA (опционально, для GPU-ускорения)
- **Coqui TTS** (XTTS v2) — синтез речи
- **Transformers** (Hugging Face) — модель Whisper
- **PyDub** + **ffmpeg** — конвертация аудио
- **SoundFile** — чтение/запись WAV
- **SpeechRecognition** — альтернативное распознавание
- **OpenAI SDK** — клиент для DeepSeek API

## Установка и запуск

### 1. Клонирование репозитория

```bash
git clone https://github.com/angrypineapple76/Text-recognition-and-playback.git
cd Text-recognition-and-playback
```

### 2. Запуск в Google Colab

Ноутбук оптимизирован для выполнения в Google Colab с GPU-ускорителем T4:

1. Откройте `Text_recognition_and_playback.ipynb` в Colab
2. Выберите среду выполнения: **Runtime → Change runtime type → T4 GPU**
3. Выполните ячейки последовательно

### 3. Локальный запуск

```bash
# Установка зависимостей
pip install coqui-tts torch torchaudio soundfile numpy
pip install transformers>=4.31.0 pydub TTS openai

# Установка ffmpeg
# Ubuntu/Debian:
sudo apt-get install -y ffmpeg
# macOS:
brew install ffmpeg
# Windows:
# Скачать с https://ffmpeg.org/download.html
```

### 4. Настройка API-ключа DeepSeek

В Google Colab:
- Добавьте секрет с именем `DP-API-Key` в настройках Colab (значок ключа в левой панели)

При локальном запуске замените в ячейке 7:
```python
API_KEY = "ваш-ключ-deepseek"
```

## Основные функции

### `generate_audio(text, speaker_wav, output_path, language="en")`
Синтезирует речь из текста с помощью XTTS, используя референсный голос.

### `prepare_audio(path)`
Конвертирует аудиофайл в формат, подходящий для Whisper: моно, 16 кГц.

### `recognize_with_whisper(audio_bytes)`
Распознаёт речь из аудио с помощью Whisper и возвращает текст.

### `postprocess(text)`
Постобработка распознанного текста: заглавная буква, пунктуация.

### `calculate_accuracy(original, recognized)`
Вычисляет точность распознавания как коэффициент Жаккара (Jaccard) между множествами слов оригинального и распознанного текстов.

## Модели

| Модель | Назначение | Источник |
|--------|------------|----------|
| **Whisper Medium** | Распознавание речи (английский) | `openai/whisper-medium` (Hugging Face) |
| **XTTS v2** | Многоязычный синтез речи | `tts_models/multilingual/multi-dataset/xtts_v2` (Coqui TTS) |
| **DeepSeek Chat** | Генерация ответов LLM | DeepSeek API (OpenAI-совместимый) |

## Референсный голос

Скачивается автоматически:
```
https://storage.yandexcloud.net/academy.ai/voice/seva_voice.wav
```

## Тестовые промпты

Ноутбук включает 10 разнообразных промптов на темы:
- Механизм внимания в Transformer
- Пример кода для OpenAI API
- Этические риски LLM
- Перевод технического текста
- Влияние генеративного AI на рынок труда
- Chain-of-Thought vs стандартные промпты
- Суммаризация статьи
- Поиск логических ошибок и оптимизация
- Prompt engineering техники
- План обучения языковым моделям

## Результаты

После выполнения пайплайна выводятся:
- Точность распознавания для каждого промпта
- Средняя точность по всем 10 промптам
- Количество промптов с точностью ≥90% и ≥95%
- Сгенерированные WAV-файлы (`output_file*.wav`)
- Ответы DeepSeek и их озвученные версии (`answer_file*.wav`)

## Автор

- GitHub: [angrypineapple76](https://github.com/angrypineapple76)

## Лицензия

Проект предназначен для образовательных и исследовательских целей.
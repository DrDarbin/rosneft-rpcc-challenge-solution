# Top 40% solution for Rosneft Proppant Check Challenge
## [Rosneft Proppant Check Challenge](https://boosters.pro/championship/RPCC/overview)

[Rating (Loft #61)](https://boosters.pro/championship/RPCC/rating)

**Описание**

Международное соревнование по анализу данных. Участникам предстоит определить размер  гранул пропанта по фотографии.

Участникам соревнования предстоит разработать алгоритм обнаружения и определения размеров гранул пропанта по фотографии.

Пропант (proppant, propping agent)  — гранулообразный материал, закачиваемый при ГРП (гидроразрыве пласта). Необходим для поддержания трещины гидроразрыва после операции ГРП в  раскрытом состоянии и обеспечения хорошей гидродинамической  связи между пластом и скважиной  .

Гранулы рассыпаны на специальном листе, содержащим референсные объекты. Фото производились на различные телефоны, в различных условиях, в съемке участвовал пропант нескольких фракций и марок. Разметкой является результат ситового анализа, представляющий собой распределение гранул по ситам.

Отличительной особенностью соревнования  является то, что решения будут использоваться в мобильном приложении. Приложение планируется использовать в местах со слабым  интернетом, поэтому модель должна работать на ограниченных ресурсах. 

На основном треке участникам предстоит отправлять свой код инференса, который будет запускаться в контейнере. Ресурсы, время и количество посылок ограничены. 

Формат сабмита на основной трек:
Решение должно посылаться в .zip архиве. Запускаемый файл script.py (убедитесь, что он находится в корне архива, некоторое ПО при архивации создает дополнительную папку).
В архиве могут находиться другие файлы, используемые вашим решением (например, модель). Предсказания следует сохранять в ./answers.csv. 

answers.csv. – таблица с двумя столбцами:
ImageId - id картинки на которой найдена гранула (id картинки - это название картинки);
prop_size - размер гранулы в мм. 
Число строк в этой таблице будет равно общему числу гранул, найденных на всех фото.

В задаче используется модифицированный образ на основе https://github.com/Kaggle/docker-python/blob/master/gpu.Dockerfile, c добавлением некоторых библиотек.
https://github.com/kilisniki/boosters.Dockerfile/blob/master/gpu.Dockerfile

Контейнер запускается со следующими опциями:

nvidia-docker run \
                -v {workspace_dir}:/workspace/  \
                -v {testing_data}:/workspace/data/:ro \
                -w /workspace/ \
                --memory 10g \
                boostersgpu \
                python script.py 1>{workspace_dir}/output1.txt 2>{workspace_dir}/output2.txt

Внутри контейнера доступа к сети нет, модули подгрузить не получится.

Расположение файлов внутри докер контейнера с запущенным примером сабмита:
/workspace# tree --filelimit 26
.
├── data
│   ├── labels
│   │  └── train.csv
│   └── test [ директория с изображениями .jpg]
│       ├── 660.jpg
│       ├── 661.jpg
│       ├── . . .
│       ├── 663.jpg
│       ├── 664.jpg
│       ├── 665.jpg
│       ├── 666.jpg
├── script.py
└── answers.csv // - сабмит записывать сюда

Свои файлы можно положить рядом с script.py 
Текущая директория workspace, в ./data/test лежат изображения. 

Ограничения на основной трек:
• vCPU: 3;
• MEM: 10 GB;
• GPU: NVIDIA T4 Tensor Core;
• gpuMEM: 16gb;
• время выполнения: 30 минут;
• размер архива: до 250мб.

Мобильный трек: 

Участникам предстоит адаптировать свое решение под работу на мобильном устройстве Xiaomi Redmi Note 9 64GB и преобразовать свои модели в onnx формат. При этом соревноваться предстоит не только в качестве, но и в скорости работы моделей на cpu.

Сабмит должен выполнять инференс моделей на тестовой выборке и  конвертацию моделей в формат в Open Neural Network Exchange (https://github.com/onnx/onnx). Модели для инференса и конвертации должны совпадать, в противном случае участники будут дисквалифицированы с обоих треков. 
Из формата onnx модели автоматически конвертируются в мобильный формат Mobile Neural Network (https://github.com/alibaba/MNN). Если модель на onnx не пройдет проверку конвертации, то решение упадет с ошибкой. 
Время работы mnn моделей на cpu замеряется на генерируемых данных. Это время учитывается в метрике трека.


Модели должны сохраняться в корне, в той же папке что и answers.csv, в формате model_*.onnx
Формат answers.csv изменился, теперь это таблица в которой для каждого фото указано распределение по ситам и число гранул на листе (новый формат можно найти в разделе Данные).

Контейнер должен запускаться со следующими опциями:

nvidia-docker run \
                -v {workspace_dir}:/workspace/  \
                -v {testing_data}:/workspace/data/:ro \
                -w /workspace/ \
                --memory 6g \
                boostersgpu \
                python script.py 1>{workspace_dir}/output1.txt 2>{workspace_dir}/output2.txt 

Расположение файлов внутри докер контейнера с запущенным примером сабмита:

/workspace# tree --filelimit 15
.
├── answers.csv [в этот файл записываются ответы]
├── data
│   ├── test [файлы *.jpg]
│       ├── 660.jpg
│       ├── 661.jpg
│       ├── . . .
│       ├── 663.jpg
│       ├── 664.jpg
│       ├── 665.jpg
│       ├── 666.jpg
│   ├── train [файлы *.jpg]
│   └── train.csv
└── script.py

Технические ограничения на мобильный трек:

• vCPU: 3;
• MEM: 6 GB;
• GPU: NVIDIA Tesla M60;
• gpuMEM: 8 GB; 
• время выполнения: 15 минут (без учета тестирования скорости работы на cpu);
• размер архива: до 250мб.

Дополнительное требование мобильного трека: 

Условием для получения приза победителем мобильного трека является подготовленное решение к работе на мобильном устройстве (совместно с мобильным разработчиком организаторов), направленное в адрес организаторов до 25.11.2020.

Официальный телеграм чат и канал чемпионата - все актуальные новости публикуются в канале, обсуждение в чате:
Телеграм чат; 
Телеграм канал. 
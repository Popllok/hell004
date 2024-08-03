<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Поиск Книг</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            background-color: #f0f0f0;
            margin: 0;
            overflow: hidden; /* Предотвращение прокрутки при анимациях */
        }
        .search-container {
            margin-bottom: 20px;
            transition: all 0.5s ease;
        }
        .search-container.small {
            position: absolute;
            top: 20px;
            left: 20px;
            width: 200px;
            margin: 0;
        }
        .search-container.small input[type="text"] {
            width: 100%;
            padding: 5px;
            font-size: 14px;
        }
        input[type="text"] {
            width: 300px;
            padding: 10px;
            font-size: 16px;
            transition: all 0.5s ease;
        }
        .book-info {
            display: none;
            text-align: center;
            position: relative;
            width: 800px;
            height: 600px; /* Высота для квадрата изображения и кнопок по бокам */
        }
        .book-info img {
            width: 400px; /* Ширина квадратного изображения */
            height: 400px; /* Высота квадратного изображения */
            border-radius: 20px; /* Закругление углов изображения */
            object-fit: cover; /* Обрезка изображения без растягивания */
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
        }
        .buttons-container {
            position: absolute;
            top: 0;
            bottom: 0;
            width: 100%;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        .left-buttons, .right-buttons {
            display: flex;
            flex-direction: column;
            justify-content: center;
        }
        .info-button {
            width: 60px; /* Размер кнопок */
            height: 60px; /* Размер кнопок */
            padding: 10px;
            font-size: 14px;
            cursor: pointer;
            border: none;
            color: white;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            text-align: center;
            transition: background-color 0.3s;
            margin: 40px; /* Увеличенный отступ между кнопками */
            opacity: 0; /* Начальное состояние для анимации */
            transform: scale(0); /* Начальный размер для анимации */
        }
        .info-button:hover {
            opacity: 0.8;
        }
        .info-button.animate-left {
            animation: fly-in-left 0.5s forwards;
        }
        .info-button.animate-right {
            animation: fly-in-right 0.5s forwards;
        }
        @keyframes fly-in-left {
            from {
                transform: translateX(-100vw) scale(0);
                opacity: 0;
            }
            to {
                transform: translateX(0) scale(1);
                opacity: 1;
            }
        }
        @keyframes fly-in-right {
            from {
                transform: translateX(100vw) scale(0);
                opacity: 0;
            }
            to {
                transform: translateX(0) scale(1);
                opacity: 1;
            }
        }
        .info-modal {
            display: none;
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background-color: white;
            padding: 20px;
            box-shadow: 0 0 10px rgba(0,0,0,0.5);
            z-index: 1000;
            max-width: 90%;
            max-height: 80%;
            overflow-y: auto;
        }
        .modal-content img {
            max-width: 100%;
            height: auto;
        }
        .close-btn {
            margin-top: 10px;
            cursor: pointer;
            padding: 10px 20px;
            background-color: #007BFF;
            color: white;
            border: none;
        }
        .modal-overlay {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0,0,0,0.5);
            z-index: 900;
        }
        @media (max-width: 600px) {
            input[type="text"] {
                width: 90%;
            }
            .info-button {
                width: 50px; /* Адаптация размеров кнопок для мобильных устройств */
                height: 50px;
                font-size: 12px;
                padding: 5px;
                margin: 25px; /* Увеличенный отступ между кнопками для мобильных устройств */
            }
            .book-info img {
                width: 250px; /* Уменьшенный размер изображения для мобильных устройств */
                height: 250px; /* Уменьшенный размер изображения для мобильных устройств */
            }
        }
    </style>
</head>
<body>

<div class="search-container" id="searchContainer">
    <input type="text" id="searchInput" placeholder="Введите название книги..." />
    <button onclick="searchBook()">Поиск</button>
</div>

<div class="book-info" id="bookInfo">
    <img src="" alt="Обложка книги" id="bookCover">
    <div class="buttons-container">
        <div class="left-buttons">
            <button class="info-button" onclick="showModal('author')" id="btnAuthor">Автор</button>
            <button class="info-button" onclick="showModal('facts')" id="btnFacts">Факты</button>
            <button class="info-button" onclick="showModal('movie')" id="btnMovie">Фильм</button>
        </div>
        <div class="right-buttons">
            <button class="info-button" onclick="showModal('author-photo')" id="btnAuthorPhoto">Фото</button>
            <button class="info-button" onclick="showModal('preview')" id="btnPreview">Книга</button>
            <button class="info-button" onclick="showModal('quiz')" id="btnQuiz">Квиз</button>
        </div>
    </div>
</div>

<div class="modal-overlay" id="modalOverlay"></div>

<div class="info-modal" id="infoModal">
    <div class="modal-content" id="modalContent"></div>
    <button class="close-btn" onclick="closeModal()">Закрыть</button>
</div>

<script>
    const books = {
        'собачье сердце': {
            cover: 'https://cdn.eksmo.ru/v2/ITD000000000819058/COVER/cover1__w600.jpg',
            backgroundColor: '#FFA07A',
            buttonColor: '#FF4500',
            author: '<h2>Михаил Булгаков</h2><p>Михаил Афанасьевич Булгаков — русский писатель, драматург и врач.</p>',
            facts: '<h2>Интересные факты</h2><p>Факт 1: "Собачье сердце" было написано в 1925 году.</p>',
            movie: '<h2>Фильм по книге</h2><p>Фильм "Собачье сердце" был снят в 1988 году режиссером Владимиром Бортко.</p>',
            authorPhoto: '<img src="https://upload.wikimedia.org/wikipedia/commons/3/3f/Mikhail_Afanasyevich_Bulgakov.jpg" alt="Фото автора">',
            preview: '<h2>Ознакомление с книгой</h2><p>Книга доступна на многих онлайн-платформах.</p>',
            quiz: '<h2>Проверь свои знания о биографии автора</h2><p>Квиз: В каком году родился Михаил Булгаков?</p>'
        },
        'война и мир': {
            cover: 'https://www.mann-ivanov-ferber.ru/assets/images/covers/33/31833/1.50x-thumb.png',
            backgroundColor: '#E6E6FA',
            buttonColor: '#6A5ACD',
            author: '<h2>Лев Толстой</h2><p>Лев Николаевич Толстой — русский писатель, мыслитель, один из величайших писателей мира.</p>',
            facts: '<h2>Интересные факты</h2><p>Факт 1: "Война и мир" была написана в 1869 году.</p>',
            movie: '<h2>Фильм по книге</h2><p>Фильм "Война и мир" был снят в 1956 году режиссером Кингом Видором.</p>',
            authorPhoto: '<img src="https://upload.wikimedia.org/wikipedia/commons/1/14/Lev_Nikolaevich_Tolstoy.jpg" alt="Фото автора">',
            preview: '<h2>Ознакомление с книгой</h2><p>Книга доступна на многих онлайн-платформах.</p>',
            quiz: '<h2>Проверь свои знания о биографии автора</h2><p>Квиз: В каком году родился Лев Толстой?</p>'
        }
    };

    function searchBook() {
        const searchInput = document.getElementById('searchInput').value.toLowerCase();
        const book = books[searchInput];
        if (book) {
            document.getElementById('bookInfo').style.display = 'block';
            document.getElementById('bookCover').src = book.cover;
            document.body.style.backgroundColor = book.backgroundColor;
            document.getElementById('searchContainer').classList.add('small');
            
            // Устанавливаем цвет кнопок
            document.querySelectorAll('.info-button').forEach(button => {
                button.style.backgroundColor = book.buttonColor;
            });
            
            // Удаляем старые анимации
            document.querySelectorAll('.left-buttons .info-button').forEach(button => button.classList.remove('animate-left'));
            document.querySelectorAll('.right-buttons .info-button').forEach(button => button.classList.remove('animate-right'));
            
            // Запускаем новые анимации
            setTimeout(() => {
                document.querySelectorAll('.left-buttons .info-button').forEach(button => button.classList.add('animate-left'));
                document.querySelectorAll('.right-buttons .info-button').forEach(button => button.classList.add('animate-right'));
            }, 10); // Небольшая задержка для запуска анимации
        } else {
            alert("Книга не найдена");
        }
    }

    function showModal(infoType) {
        const searchInput = document.getElementById('searchInput').value.toLowerCase();
        const book = books[searchInput];
        let content = book[infoType] || '<p>Информация не найдена</p>';
        document.getElementById('modalContent').innerHTML = content;
        document.getElementById('infoModal').style.display = 'block';
        document.getElementById('modalOverlay').style.display = 'block';
    }

    function closeModal() {
        document.getElementById('infoModal').style.display = 'none';
        document.getElementById('modalOverlay').style.display = 'none';
    }
</script>

</body>
</html>

# Web App выбора маршрута

`index.html` — Telegram Web App с картой OpenStreetMap/Leaflet. Пассажир
ставит две точки (подача и назначение) и отправляет координаты боту
через `Telegram.WebApp.sendData`.

## Требования

- **HTTPS-хостинг.** Telegram не откроет Web App по `http://` или `file://`.
- Доступ в интернет для Leaflet CDN и тайлов OpenStreetMap.

## Быстрый деплой

1. Выложите содержимое папки `webapp/` на любой статический HTTPS-хостинг:
   GitHub Pages, Cloudflare Pages, Netlify, Vercel или собственный сервер.
2. Убедитесь, что `index.html` открывается по HTTPS и карта загружается.
3. В `.env` основного бота укажите:

   ```env
   WEBAPP_URL=https://ваш-домен.example/webapp/
   ```

4. Перезапустите бота: `python main.py`.
5. Проверьте настройку: `python check_deploy.py` — строка WEBAPP_URL
   должна стать `✅`.

## Проверка перед публикацией

```powershell
python check_deploy.py
```

Если Telegram не открывает Web App, привяжите домен к боту через
@BotFather (`/setdomain`) и проверьте, что сертификат HTTPS валиден.

Если `WEBAPP_URL` не задан, бот работает в резервном режиме: два запроса
геолокации (подача и назначение) через обычные кнопки Telegram.

## Локальный просмотр

Для проверки вёрстки в браузере:

```powershell
cd "C:\Users\Тима\AppData\Local\Cline\women-taxi-bot\webapp"
python -m http.server 8080
```

Откройте `http://127.0.0.1:8080/`. Это только предпросмотр: Telegram
Web App требует HTTPS, поэтому кнопка в боте появится после деплоя.

## Что отправляется боту

```json
{
  "pickup": {"lat": 55.796, "lon": 49.108},
  "destination": {"lat": 55.75, "lon": 49.2}
}
```

Бот проверяет координаты, определяет адреса через Nominatim и передаёт
маршрут в заказ.

## Ограничения

- Публичные тайлы OpenStreetMap и Nominatim не дают SLA и имеют лимиты.
  Для production используйте платный геокодер/тайлы или собственный сервер.
- `Telegram.WebApp.sendData` ограничен 4096 байтами; текущий JSON намного меньше.
- Если пользователь закроет Web App без отправки, заказ останется на шаге
  маршрута; кнопку можно нажать снова.

# docker-cleanup
улучшенный скрипт «умной очистки Docker», который сначала проверяет, что у вас есть, и предлагает три варианта: частичная очистка, полная очистка или отмена.
____________________

Если нет возможности скопировать, создайте файл командой nano docker-cleanup.sh  и вставьте в него код что ниже:
<pre> ```

#!/bin/bash

echo "=== Проверяем, что занимает место в Docker ==="
echo
echo "Контейнеры:"
docker ps -a
echo
echo "Образы:"
docker images
echo
echo "Volume'ы:"
docker volume ls
echo

echo "Что вы хотите сделать?"
echo "1) Частичная очистка (удалить неиспользуемые контейнеры, образы и volume'ы)"
echo "2) Полная очистка (удалить ВСЁ: контейнеры, образы, volume'ы, сети)"
echo "3) Отмена"

read -p "Выберите (1/2/3): " choice

case "$choice" in
  1)
    echo "Выполняем частичную очистку..."
    docker system prune -a --volumes
    ;;
  2)
    echo "⚠️  ПОЛНАЯ ОЧИСТКА: ВСЕ данные Docker будут удалены!"
    read -p "Вы уверены? (y/N): " confirm
    if [[ "$confirm" == "y" || "$confirm" == "Y" ]]; then
      echo "Останавливаем Docker..."
      sudo systemctl stop docker
      echo "Удаляем все данные Docker..."
      sudo rm -rf /var/lib/docker/*
      echo "Запускаем Docker заново..."
      sudo systemctl start docker
      echo "Очистка завершена. Docker запущен как новый."
    else
      echo "Отмена."
    fi
    ;;
  3)
    echo "Отмена."
    ;;
  *)
    echo "Неверный ввод. Завершение."
    ;;
esac

echo
echo "Проверяем, сколько места теперь свободно:"
df -h /


``` </pre>

-------------------------------
Как использовать:
-------------------------------
Сохраните скрипт:

nano docker-cleanup.sh
Вставьте код, сохраните (Ctrl+O, Enter, Ctrl+X).
-------------------------
Сделайте исполняемым:

chmod +x docker-cleanup.sh
-------------------------------

Запустите:
./docker-cleanup.sh

###############################################################################
# Replace
# __GUNICORN_FILE_NAME__ the name of the gunicorn file you want
# __YOUR_USER__ your user name
# __APP_ROOT_NAME__ the folder name of your project
# __WSGI_FOLDER_NAME__ the folder name where you find a file called wsgi.py
#
###############################################################################
# Criando o arquivo __GUNICORN_FILE_NAME__.socket
sudo nano /etc/systemd/system/__GUNICORN_FILE_NAME__.socket

###############################################################################
# Conteúdo do arquivo
[Unit]
Description=gunicorn __GUNICORN_FILE_NAME__ socket

[Socket]
ListenStream=/run/__GUNICORN_FILE_NAME__.socket

[Install]
WantedBy=sockets.target

###############################################################################
# Criando o arquivo __GUNICORN_FILE_NAME__.service
sudo nano /etc/systemd/system/__GUNICORN_FILE_NAME__.service

###############################################################################
# Conteúdo do arquivo
[Unit]
Description=Gunicorn daemon (You can change if you want)
Requires=__GUNICORN_FILE_NAME__.socket
After=network.target

[Service]
User=__YOUR_USER__
Group=www-data
Restart=on-failure
# EnvironmentFile=/home/__YOUR_USER__/__APP_ROOT_NAME__/.env
WorkingDirectory=/home/__YOUR_USER__/__APP_ROOT_NAME__
# --error-logfile --enable-stdio-inheritance --log-level and --capture-output
# are all for debugging purposes.
ExecStart=/home/__YOUR_USER__/__APP_ROOT_NAME__/venv/bin/gunicorn \
          --error-logfile /home/__YOUR_USER__/__APP_ROOT_NAME__/gunicorn-error-log \
          --enable-stdio-inheritance \
          --log-level "debug" \
          --capture-output \
          --access-logfile - \
          --workers 6 \
          --bind unix:/run/__GUNICORN_FILE_NAME__.socket \
          __WSGI_FOLDER_NAME__.wsgi:application

[Install]
WantedBy=multi-user.target

###############################################################################
# Ativando
sudo systemctl start __GUNICORN_FILE_NAME__.socket
sudo systemctl enable __GUNICORN_FILE_NAME__.socket

# Checando
sudo systemctl status __GUNICORN_FILE_NAME__.socket
curl --unix-socket /run/__GUNICORN_FILE_NAME__.socket localhost
sudo systemctl status __GUNICORN_FILE_NAME__

# Restarting
sudo systemctl restart __GUNICORN_FILE_NAME__.service
sudo systemctl restart __GUNICORN_FILE_NAME__.socket
sudo systemctl restart __GUNICORN_FILE_NAME__

# After changing something
sudo systemctl daemon-reload

# Debugging
sudo journalctl -u __GUNICORN_FILE_NAME__.service
sudo journalctl -u __GUNICORN_FILE_NAME__.socket
name: SSH Ngrok with Telegram Notify

on:
  workflow_dispatch:
  schedule:
    - cron: '0 */6 * * *'

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
    - name: Download ngrok
      run: |
        wget https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-linux-amd64.tgz
        tar xvzf ngrok-v3-stable-linux-amd64.tgz
        chmod +x ngrok
    
    - name: Auth ngrok
      run: ./ngrok authtoken ${{ secrets.NGROK_TOKEN }}
    
    - name: Setup SSH
      run: |
        sudo apt-get update
        sudo apt-get install -y openssh-server jq
        sudo systemctl start ssh
        sudo systemctl enable ssh
    
    - name: Set User Password
      run: |
        echo "runner:${{ secrets.SSH_PASSWORD }}" | sudo chpasswd
    
    - name: Start ngrok and notify
      run: |
        ./ngrok tcp 22 > /dev/null &
        sleep 10
        
        NGROK_URL=$(curl -s http://127.0.0.1:4040/api/tunnels | jq -r '.tunnels[0].public_url')
        NGROK_HOST=$(echo $NGROK_URL | sed 's|tcp://||' | cut -d':' -f1)
        NGROK_PORT=$(echo $NGROK_URL | sed 's|tcp://||' | cut -d':' -f2)
        
        # Kirim info ke Telegram (opsional)
        MESSAGE="🚀 SSH Ready!%0AHost: $NGROK_HOST%0APort: $NGROK_PORT%0ACommand: ssh runner@$NGROK_HOST -p $NGROK_PORT"
        curl -s "https://api.telegram.org/bot${{ secrets.TELEGRAM_BOT_TOKEN }}/sendMessage?chat_id=${{ secrets.TELEGRAM_CHAT_ID }}&text=$MESSAGE" > /dev/null
        
        echo "=========================================="
        echo "ssh runner@$NGROK_HOST -p $NGROK_PORT"
        echo "=========================================="
        
        sleep 21000

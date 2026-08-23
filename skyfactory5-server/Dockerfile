# Temel imaj olarak Debian Bookworm (slim versiyonu) kullanıyoruz
FROM debian:bookworm-slim

# Java 17, arşiv ayıklamak için unzip ve indirme araçlarını kuruyoruz
RUN apt-get update && \
    apt-get install -y openjdk-17-jre-headless unzip wget && \
    rm -rf /var/lib/apt/lists/*

# Sunucu dosyalarının bulunacağı dizini oluşturuyoruz
WORKDIR /server

# Sunucunun çalışması için gerekli Minecraft portunu dışarı açıyoruz
EXPOSE 25565

# Otomatik kurulum ve başlatma betiğini hazırlıyoruz
RUN echo '#!/bin/bash\n\
    \n\
    # Eğer server daha önce kurulmadıysa ve zip dosyası atıldıysa otomatik kur\n\
    if [ ! -f "run.sh" ] && [ ! -f "run.bat" ]; then\n\
    ZIP_FILE=$(find . -maxdepth 1 -name "*.zip" | head -n 1)\n\
    if [ -n "$ZIP_FILE" ]; then\n\
    echo "Sunucu ZIP dosyasi bulundu: $ZIP_FILE. Arsiv aciliyor..."\n\
    unzip -o "$ZIP_FILE"\n\
    \n\
    # Linux kurulum betigine (install.sh) calistirma izni ver \n\
    if [ -f "install.sh" ]; then\n\
    chmod +x install.sh\n\
    \n\
    # HeadlessException hatasini onlemek icin install.sh icindeki java komutuna\n\
    # -Djava.awt.headless=true parametresini ekliyoruz.\n\
    # Eger installer jar dosyasi barindiriyorsa, onu baslatirken bu komutu zorluyoruz.\n\
    sed -i '"'"'s/java -jar/java -Djava.awt.headless=true -jar/g'"'"' install.sh\n\
    \n\
    echo "Kurulum (install.sh) baslatiliyor..."\n\
    ./install.sh\n\
    fi\n\
    else\n\
    echo "HATA: CurseForge dan indirdiginiz SkyFactory 5 server zip dosyasini data/ klasorune atmalisiniz!"\n\
    sleep 30\n\
    fi\n\
    fi\n\
    \n\
    # EULA otomatik onaylanıyor\n\
    echo "eula=true" > eula.txt\n\
    \n\
    # RAM ayarlarını docker-compose üzerinden alıp dosyaya yazıyoruz\n\
    echo "-Xmx${SERVER_RAM} -Djava.awt.headless=true" > user_jvm_args.txt\n\
    \n\
    # Sunucuyu başlatma mantığı\n\
    if [ -f "run.sh" ]; then\n\
    chmod +x run.sh\n\
    ./run.sh\n\
    else\n\
    FORGE_JAR=$(find . -maxdepth 1 -name "forge-*.jar" | head -n 1)\n\
    if [ -z "$FORGE_JAR" ]; then\n\
    echo "Sunucu baslatilamadi, jar dosyasi bulunamadi. Lutfen dosyalari kontrol edin."\n\
    sleep 60\n\
    else\n\
    java @user_jvm_args.txt -Djava.awt.headless=true -jar $FORGE_JAR nogui\n\
    fi\n\
    fi' > /start.sh

RUN chmod +x /start.sh

CMD ["/start.sh"]
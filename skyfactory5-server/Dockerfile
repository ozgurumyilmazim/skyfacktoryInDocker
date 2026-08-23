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
    # Eğer server daha önce kurulmadıysa (forge-*universal*.jar yoksa) ve zip dosyası atıldıysa otomatik kur\n\
    if [ ! -f "run.sh" ] && [ -z "$(find . -maxdepth 1 -name "forge-*-universal.jar" | head -n 1)" ]; then\n\
    ZIP_FILE=$(find . -maxdepth 1 -name "*.zip" | head -n 1)\n\
    if [ -n "$ZIP_FILE" ]; then\n\
    echo "Sunucu ZIP dosyasi bulundu: $ZIP_FILE. Arsiv aciliyor..."\n\
    unzip -o "$ZIP_FILE"\n\
    \n\
    # Forge Installer jar dosyasını buluyoruz\n\
    INSTALLER_JAR=$(find . -maxdepth 1 -name "forge-*-installer.jar" | head -n 1)\n\
    if [ -n "$INSTALLER_JAR" ]; then\n\
    echo "Forge Installer bulundu. Sunucu kuruluyor..."\n\
    # Kurulumu headless argümanı ve --installServer bayrağı ile zorla çalıştırıyoruz\n\
    java -Djava.awt.headless=true -jar "$INSTALLER_JAR" --installServer\n\
    else\n\
    echo "UYARI: Forge installer jar bulunamadi! install.sh deneniyor..."\n\
    if [ -f "install.sh" ]; then\n\
    chmod +x install.sh\n\
    sed -i '"'"'s/java -jar/java -Djava.awt.headless=true -jar/g'"'"' install.sh\n\
    ./install.sh\n\
    fi\n\
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
    # Çalıştırılabilir forge jar dosyasını bul (installer olmayan)\n\
    FORGE_JAR=$(find . -maxdepth 1 -name "forge-*.jar" ! -name "*-installer.jar" | head -n 1)\n\
    if [ -z "$FORGE_JAR" ]; then\n\
    echo "Sunucu baslatilamadi, calistirilabilir forge jar dosyasi bulunamadi!"\n\
    sleep 60\n\
    else\n\
    echo "Sunucu baslatiliyor: $FORGE_JAR"\n\
    java @user_jvm_args.txt -Djava.awt.headless=true -jar "$FORGE_JAR" nogui\n\
    fi\n\
    fi' > /start.sh

RUN chmod +x /start.sh

CMD ["/start.sh"]
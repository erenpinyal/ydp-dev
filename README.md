# *Adım 1: **Projenin Konusu ve Amacı**

Bu proje, "Cleaned vs Dirty" veri setini kullanarak temiz ve kirli nesneleri ayırarak görüntülerin sınıflandırılması için bulunmakta.

### **Adım 2: Kullanılan platform**

Google Colab

### **Adım 3: Veri Seti **

Veri setini önce plates.zip dosyasından çıkardım ve fonksiyonla ayırdım

def split_data(input_folder, output_folder, split_ratio=(0.8, 0.1, 0.1)):
    import os, random, shutil

    # Eğitim, test ve doğrulama klasörleri oluştur
    for split in ['train', 'val', 'test']:
        os.makedirs(os.path.join(output_folder, split, 'cleaned'), exist_ok=True)
        os.makedirs(os.path.join(output_folder, split, 'dirty'), exist_ok=True)
    
    # Dosyaları yükle ve karıştır
    files = os.listdir(input_folder)
    random.shuffle(files)
    
    train_split = int(split_ratio[0] * len(files))
    val_split = int(split_ratio[1] * len(files)) + train_split
    
    # Dosyaları klasörlere kopyala
    for i, file in enumerate(files):
        if i < train_split:
            shutil.copy(os.path.join(input_folder, file), os.path.join(output_folder, 'train', file))
        elif i < val_split:
            shutil.copy(os.path.join(input_folder, file), os.path.join(output_folder, 'val', file))
        else:
            shutil.copy(os.path.join(input_folder, file), os.path.join(output_folder, 'test', file))


Bu işlem sonucunda, veri seti aşağıdaki yapıya sahip oldu:

bash
/working/
    train/
        cleaned/
        dirty/
    val/
        cleaned/
        dirty/
    test/
        cleaned/
        dirty/

### **Adım 4: model eğitimi**

YOLO modelini eğitmek için yolov5n kullandım

from ultralytics import YOLO


model = YOLO('yolov5.pt')
results = model.train(data='data.yaml', epochs=100, imgsz=640)

### **Adım 5: Sonuçlar**

##### **genel doğruluk: %95**

###### **confusion matrix**

Gerçek (Actual) \ Tahmin (Predicted)   Cleaned   Dirty
Cleaned                                16         4
Dirty                                  50        694

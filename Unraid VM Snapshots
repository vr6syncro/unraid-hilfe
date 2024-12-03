## **Snapshot-Verwaltung mit QEMU/libvirt und Unraid 7**

### **1. Grundlagen**  
Snapshots speichern den Zustand einer VM zu einem bestimmten Zeitpunkt. Es gibt zwei Hauptarten:  
- **Interne Snapshots:** Gespeichert direkt in der `.qcow2`-Datei.  
- **Externe Snapshots:** Änderungen werden in separaten Dateien (Overlay) gespeichert.

---

### **2. Befehle und Platzhalter**
- **`<domain>`:** Name, ID oder UUID der VM (z. B. `testvm`).  
- **`<disk>`:** Pfad zur Festplatte (z. B. `/var/lib/libvirt/images/vdisk1.qcow2`).  
- **`<snapshot_name>`:** Name des Snapshots.  
- **`<destination_file>`:** Zielpfad für eine Kopie.

---

### **3. Operationen**

#### **a. Snapshot erstellen**  
**Beschreibung:** Erstellt einen Snapshot der VM.  
**Optionen:**  
- Nur Festplatte:  
  ```
  virsh snapshot-create-as <domain> <snapshot_name> --disk-only
  ```
- Festplatte und RAM:  
  ```
  virsh snapshot-create-as <domain> <snapshot_name> --memspec
  ```

---

#### **b. Snapshot zurücksetzen (Revert)**  
**Beschreibung:** Stellt die VM in den Zustand des Snapshots zurück.  
**Nutzen:** Rückkehr zu einem vorherigen Zustand.  
**Beispiel:**  
Die VM wird auf den Snapshot `snapshot1` zurückgesetzt.  
```
virsh snapshot-revert <domain> snapshot1
```

---

#### **c. Block Commit (Änderungen zusammenführen)**  
**Beschreibung:** Überträgt Änderungen aus Overlay-Dateien zurück in das Basis-Image.  
**Voraussetzung:** VM muss **ausgeschaltet** sein.  
**Nutzen:** Reduziert die Snapshot-Kette und vereinfacht die Verwaltung.  
**Beispiel:**  
```
Original <- Snap1 <- Snap2*
```
Wird zu:  
```
Original^ <- Snap2*+
```
**Befehl:**  
```
virsh blockcommit <domain> <disk> --wait
```

---

#### **d. Block Pull (Daten in Overlay-Datei ziehen)**  
**Beschreibung:** Konsolidiert Änderungen aus dem Basis-Image in die Overlay-Datei.  
**Voraussetzung:** VM muss **laufen**.  
**Nutzen:** Macht Overlay-Dateien unabhängig von der Basisdatei.  
**Beispiel:**  
```
Original <- Snap1 <- Snap2*
```
Wird zu:  
```
Original <- Snap2*+^
```
**Befehl:**  
```
virsh blockpull <domain> <disk> --wait
```

---

#### **e. Block Copy (Snapshot-Kopie erstellen)**  
**Beschreibung:** Erstellt eine vollständige Kopie der VM inklusive Änderungen.  
**Nutzen:** Migration oder Backup der VM.  
**Beispiel:**  
```
Original <- Snap1 <- Snap2*
```
Wird zu:  
```
New^*
```
**Befehl:**  
```
virsh blockcopy <domain> <disk> <destination_file> --wait
```

---

### **4. Verwaltung von Festplatten mit `qemu-img`**

#### **Festplatteninformationen anzeigen**  
**Befehl:**  
```
qemu-img info <dateiname>
```
**Beispielausgabe:**  
```
image: vdisk1.qcow2
file format: qcow2
virtual size: 10G (10737418240 bytes)
disk size: 2.5G
```

---

#### **Neue Overlay-Datei erstellen**  
**Befehl:**  
```
qemu-img create -f qcow2 -b <backing_file> <new_overlay_file>
```

---

#### **Änderungen zusammenführen (Commit)**  
**Befehl:**  
```
qemu-img commit <dateiname>
```

---

### **5. Beispiele für typische Szenarien**

#### **Snapshot während des Betriebs (mit RAM):**
1. **Snapshot erstellen:**  
   ```
   virsh snapshot-create-as <domain> <snapshot_name> --disk-only --memspec
   ```
2. **Snapshot zurücksetzen:**  
   ```
   virsh snapshot-revert <domain> <snapshot_name>
   ```

---

#### **Snapshot-Kette bereinigen:**
1. **Änderungen aus Snapshots zusammenführen:**  
   ```
   virsh blockcommit <domain> <disk> --wait
   ```
2. **Alte Snapshots entfernen:**  
   ```
   rm <snapshot_file>
   ```

---

#### **Migration oder Backup:**
1. **Kopie der VM erstellen:**  
   ```
   virsh blockcopy <domain> <disk> <destination_file> --wait
   ```

---

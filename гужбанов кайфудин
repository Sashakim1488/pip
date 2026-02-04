Glavnoe
import sys
import os
import csv
from datetime import datetime
import cv2
from PyQt5.QtWidgets import QApplication, QWidget, QLabel, QLineEdit, QPushButton, QMessageBox
from PyQt5.QtCore import QTimer
from PyQt5.QtGui import QImage, QPixmap

class Glavnoe(QWidget):
    def __init__(self):
        super().__init__()
        self.camera = None
        self.timer = QTimer()
        self.initUI()

    def initUI(self):
        self.setGeometry(100, 100, 1000, 600)
        self.setWindowTitle('Нейрободр')
        self.setStyleSheet("background-color: #e8f5e9;")

        self.lbl_title = QLabel('НЕЙРОБОДР', self)
        self.lbl_title.setStyleSheet("color: #2e7d32; font-weight: bold; font-size: 24px;")
        self.lbl_title.move(450, 20)

        self.lbl_subtitle = QLabel('Программа для мониторинга состояния водителей', self)
        self.lbl_subtitle.move(350, 60)
        self.lbl_subtitle.setStyleSheet("color: #2e7d32; font-size: 14px;")

        self.lbl_reg = QLabel('Регистрация оператора', self)
        self.lbl_reg.move(50, 110)
        self.lbl_reg.setStyleSheet("font-weight: bold; font-size: 16px;")

        QLabel('Фамилия:', self).move(50, 150)
        self.fam = QLineEdit(self)
        self.fam.move(50, 180)
        self.fam.resize(180, 30)
        self.fam.setStyleSheet("border: 1px solid #4caf50; padding: 5px;")

        QLabel('Имя:', self).move(50, 220)
        self.name = QLineEdit(self)
        self.name.move(50, 250)
        self.name.resize(180, 30)
        self.name.setStyleSheet("border: 1px solid #4caf50; padding: 5px;")

        QLabel('Отчество:', self).move(50, 290)
        self.otch = QLineEdit(self)
        self.otch.move(50, 320)
        self.otch.resize(180, 30)
        self.otch.setStyleSheet("border: 1px solid #4caf50; padding: 5px;")

        QLabel('Возраст:', self).move(50, 360)
        self.age = QLineEdit(self)
        self.age.move(50, 390)
        self.age.resize(80, 30)
        self.age.setStyleSheet("border: 1px solid #4caf50; padding: 5px;")

        self.lbl_camera = QLabel('Камера', self)
        self.lbl_camera.move(350, 110)
        self.lbl_camera.setStyleSheet("font-weight: bold; font-size: 16px;")

        self.camera_label = QLabel(self)
        self.camera_label.move(350, 150)
        self.camera_label.resize(240, 180)
        self.camera_label.setStyleSheet("border: 2px solid #4caf50; background-color: black;")

        self.btn_camera = QPushButton('Запустить камеру', self)
        self.btn_camera.move(400, 340)
        self.btn_camera.resize(150, 30)
        self.btn_camera.setStyleSheet("""
            QPushButton {
                background-color: #2196F3; 
                color: white; 
                border-radius: 3px;
                padding: 5px;
                border: none;
            }
            QPushButton:hover {
                background-color: #1976D2;
            }
        """)
        self.btn_camera.clicked.connect(self.start_camera)

        self.lbl_info = QLabel('Информационный блок', self)
        self.lbl_info.move(650, 110)
        self.lbl_info.setStyleSheet("font-weight: bold; font-size: 16px;")

        self.lbl_status = QLabel('Оператор не определен', self)
        self.lbl_status.move(650, 150)
        self.lbl_status.setStyleSheet("color: #e74c3c; font-size: 14px;")

        self.lbl_id_status = QLabel('ID не присвоен', self)
        self.lbl_id_status.move(650, 180)
        self.lbl_id_status.setStyleSheet("font-size: 14px;")

        self.lbl_desc = QLabel('Для регистрации:\n1. Заполните поля слева\n2. Запустите камеру\n3. Нажмите "Зарегистрировать"', self)
        self.lbl_desc.move(650, 250)
        self.lbl_desc.resize(300, 100)
        self.lbl_desc.setStyleSheet("font-size: 12px; color: #666;")

        self.btn_register = QPushButton('Зарегистрировать', self)
        self.btn_register.move(400, 400)
        self.btn_register.resize(200, 50)

        self.btn_register.clicked.connect(self.save_data)

        self.timer.timeout.connect(self.update_camera)

    def start_camera(self):
        self.camera = cv2.VideoCapture(0)
        self.timer.start(30)

    def update_camera(self):
        if self.camera:
            ret, frame = self.camera.read()
            if ret:
                rgb = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
                rgb = cv2.resize(rgb, (240, 180))
                h, w, ch = rgb.shape
                qt_img = QImage(rgb.data, w, h, ch * w, QImage.Format_RGB888)
                self.camera_label.setPixmap(QPixmap.fromImage(qt_img))

    def save_data(self):
        # ID
        if os.path.exists('operators_db.csv'):
            with open('operators_db.csv', 'r') as f:
                lines = f.readlines()
                user_id = f"{len(lines):05d}"
        else:
            user_id = "00001"

        if self.camera:
            ret, frame = self.camera.read()
            if ret:
                cv2.imwrite(f"photo_{user_id}.jpg", frame)

        with open('operators_db.csv', 'a', newline='') as f:
            writer = csv.writer(f)
            writer.writerow([
                user_id,
                self.fam.text(),
                self.name.text(),
                self.otch.text(),
                self.age.text(),
                datetime.now().strftime("%d-%m-%Y"),
                datetime.now().strftime("%H:%M:%S"),
                '00:00:00'
            ])

        self.lbl_status.setText('Оператор определен')
        self.lbl_status.setStyleSheet("color: #27ae60; font-size: 14px;")
        self.lbl_id_status.setText(f'ID: {user_id}')
        self.lbl_desc.setText(f'Регистрация завершена!\nID: {user_id}')

        QMessageBox.information(self, 'Успех', f'Регистрация завершена! ID: {user_id}')

        if self.camera:
            self.camera.release()
            self.timer.stop()

        self.close()

        if os.path.exists("AuthWindow.py"):
            os.system(f"python AuthWindow.py {user_id}")

if __name__ == '__main__':
    app = QApplication(sys.argv)
    ex = Glavnoe()
    ex.show()
    sys.exit(app.exec_())






AutoReg
import sys
import subprocess
from PyQt5.QtWidgets import QApplication, QWidget, QPushButton


class AutoReg(QWidget):
    def __init__(self):
        super().__init__()
        self.initUI()

        def open_auth(self):
            auth_window = AuthWindow()
            auth_window.show()
            self.hide()

    def initUI(self):
        self.setGeometry(300, 300, 300, 300)
        self.setWindowTitle('Авторизация')

        self.btn = QPushButton('Регистрация', self)
        self.btn.move(100, 100)
        self.btn.clicked.connect(self.open_registration)

        self.btn2 = QPushButton('Авторизация', self)
        self.btn2.move(100, 150)
        self.btn2.clicked.connect(self.open_auth)

    def open_registration(self):
        self.close()
        subprocess.Popen([sys.executable, "Glavnoe.py"])

    def open_auth(self):
        self.close()
        subprocess.Popen([sys.executable, "AuthWindow.py"])

if __name__ == '__main__':
    app = QApplication(sys.argv)
    ex = AutoReg()
    ex.show()
    sys.exit(app.exec_())



    

AuthWindow
import csv
import sys
import os

from PyQt5.QtWidgets import QApplication, QWidget, QLabel, QLineEdit, QPushButton, QMessageBox


class AuthWindow(QWidget):
    def __init__(self, *args):
        super().__init__()
        self.suggested_id = args[-1] if args else ""
        self.initUI(args)

    def initUI(self, args):
        self.setGeometry(300, 300, 400, 200)
        self.setWindowTitle('Авторизация')

        label = QLabel('Введите ID оператора:', self)
        label.move(50, 50)

        self.id_input = QLineEdit(self)
        self.id_input.move(50, 80)
        self.id_input.resize(200, 30)

        if self.suggested_id:
            self.id_input.setText(self.suggested_id)
            label2 = QLabel(f'Ваш ID из регистрации: {self.suggested_id}', self)
            label2.move(50, 30)
            label2.setStyleSheet("color: #4caf50;")

        btn = QPushButton('Войти', self)
        btn.move(150, 120)
        btn.clicked.connect(self.auth)

    def auth(self):
        user_id = self.id_input.text()

        if os.path.exists('operators_db.csv'):
            with open('operators_db.csv', 'r', newline='') as file:
                reader = csv.reader(file)
                found = False
                try:
                    next(reader)
                except:
                    pass

                for row in reader:
                    if row and row[0] == user_id:
                        found = True
                        break

                if not found:
                    QMessageBox.warning(self, 'Ошибка', 'ID не найден в базе')
                    return
        self.close()
        if os.path.exists("InfoForm.py"):
            os.system(f"{sys.executable} InfoForm.py {user_id}")

if __name__ == '__main__':
    app = QApplication(sys.argv)

    if len(sys.argv) > 1:
        ex = AuthWindow(sys.argv[1])
    else:
        ex = AuthWindow()

    ex.show()
    sys.exit(app.exec_())







infoForm
import sys
import os
import csv
from datetime import datetime
from PyQt5.QtWidgets import QApplication, QWidget, QLabel, QPushButton, QMessageBox


class InfoForm(QWidget):
    def __init__(self, *args):
        super().__init__()
        self.user_id = args[-1] if args else ""
        self.initUI()

    def initUI(self):
        self.setGeometry(300, 300, 800, 500)
        self.setWindowTitle('Нейрободр')
        self.setStyleSheet("background-color: #e8f5e9;")

        self.lbl_title = QLabel('НЕЙРОБОДР', self)
        self.lbl_title.setStyleSheet("color: #2e7d32; font-weight: bold; font-size: 24px;")
        self.lbl_title.move(350, 20)

        self.lbl_subtitle = QLabel('Программа для мониторинга состояния водителей', self)
        self.lbl_subtitle.move(250, 60)
        self.lbl_subtitle.setStyleSheet("color: #2e7d32; font-size: 14px;")

        self.lbl_info = QLabel('Информация оператора', self)
        self.lbl_info.move(50, 110)
        self.lbl_info.setStyleSheet("font-weight: bold; font-size: 16px;")

        full_name = "Не найдено"
        age = ""

        if os.path.exists('operators_db.csv'):
            with open('operators_db.csv', 'r', newline='') as file:
                reader = csv.reader(file)
                next(reader, None)
                for row in reader:
                    if row and row[0] == self.user_id:
                        full_name = f"{row[1]} {row[2]} {row[3]}"
                        age = row[4]
                        date = row[5]

                        self.lbl_name = QLabel(f"ФИО: {full_name}", self)
                        self.lbl_name.move(50, 140)

                        self.lbl_age = QLabel(f"Возраст: {age} лет", self)
                        self.lbl_age.move(50, 170)

                        self.lbl_date = QLabel(f"Дата регистрации: {date}", self)
                        self.lbl_date.move(50, 200)
                        break

        self.lbl_ident = QLabel('Идентификация', self)
        self.lbl_ident.move(300, 110)
        self.lbl_ident.setStyleSheet("font-weight: bold; font-size: 16px;")

        self.lbl_id = QLabel(f'ID: {self.user_id}', self)
        self.lbl_id.move(300, 140)
        self.lbl_id.setStyleSheet("font-size: 14px; color: #2c3e50;")

        self.lbl_block = QLabel('Информационный блок', self)
        self.lbl_block.move(550, 110)
        self.lbl_block.setStyleSheet("font-weight: bold; font-size: 16px;")

        self.lbl_status = QLabel('Оператор определен', self)
        self.lbl_status.move(550, 140)
        self.lbl_status.setStyleSheet("color: #27ae60; font-size: 14px;")

        self.lbl_status_id = QLabel(f'ID: {self.user_id}', self)
        self.lbl_status_id.move(550, 170)
        self.lbl_status_id.setStyleSheet("font-size: 14px;")

        current_time = datetime.now()

        self.lbl_datetime = QLabel(f'Дата/время: {current_time.strftime("%d.%m.%Y / %H:%M:%S")}', self)
        self.lbl_datetime.move(50, 230)

        self.lbl_start_time = QLabel(f'Время запуска ПО: {current_time.strftime("%H:%M:%S")}', self)
        self.lbl_start_time.move(50, 260)

        self.lbl_drive_time = QLabel('Время в дороге: 00:00:00', self)
        self.lbl_drive_time.move(50, 290)

        self.lbl_remaining_time = QLabel('Оставшееся время: 09:00:00', self)
        self.lbl_remaining_time.move(50, 320)

        self.btn_next = QPushButton('Далее', self)
        self.btn_next.move(350, 380)
        self.btn_next.resize(150, 50)
        self.btn_next.setStyleSheet("""
            QPushButton {
                background-color: #4caf50; 
                color: white; 
                font-weight: bold; 
                border-radius: 5px;
                padding: 10px;
                border: none;
                font-size: 16px;
            }
            QPushButton:hover {
                background-color: #45a049;
            }
        """)
        self.btn_next.clicked.connect(self.next_action)

    def next_action(self):
        QMessageBox.information(self, 'Запуск', f'Программа запущена для ID: {self.user_id}')

if __name__ == '__main__':
    app = QApplication(sys.argv)
    user_id = sys.argv[1] if len(sys.argv) > 1 else "00001"
    ex = InfoForm(user_id)
    ex.show()
    sys.exit(app.exec_())

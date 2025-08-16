from PyQt6 import QtWidgets, uic,QtCore
from PyQt6.QtCore import Qt
from PyQt6.QtGui import QKeyEvent
from PyQt6.QtWidgets import QMessageBox,QPushButton
import sys
import json
import os

class LoginWindow(QtWidgets.QMainWindow):
    def __init__(self):
        super().__init__()
        uic.loadUi("Login.ui", self)  

        self.loginButton.clicked.connect(self.login)  
        self.registerButton.clicked.connect(self.open_register)  
        self.load_credentials()

    def login(self):
        username = self.usernameInput.text()
        password = self.passwordInput.text()
        email    = self.emailInput.text()

        if not username or not password or not email:
            QMessageBox.warning(self, "Cảnh báo", "Vui lòng nhập đầy đủ thông tin.")
            return

        if self.validate_credentials(username, password, email):
            QMessageBox.information(self, "Thành công", "Đăng nhập thành công!")
            self.open_main_window()  
            self.close()  

        else:
            QMessageBox.critical(self, "Lỗi", "Sai mật khẩu, tên đăng nhập, hoặc email.")

    def validate_credentials(self, username, password, email):
        if os.path.exists('credentials.json'):
            with open('credentials.json', 'r') as file:
                credentials = json.load(file)
                user_info = credentials.get(username)
                if user_info and user_info['password'] == password and user_info['email'] == email:
                    return True
        return False

    def open_register(self):
        self.hide()
        self.registration_window = RegisterWindow() 
        self.registration_window.show() 

    def load_credentials(self):
        if os.path.exists('credentials.json'):
            with open('credentials.json', 'r') as file:
                credentials = json.load(file)
                self.usernameInput.setText(credentials.get("last_username", ""))

    def open_main_window(self):
        self.main_window = MainWindow() 
        self.main_window.show()



class RegisterWindow(QtWidgets.QMainWindow):
    def __init__(self):
        super().__init__()
        uic.loadUi("Register.ui", self)  

        # Kết nối các nút
        self.registerButton.clicked.connect(self.register)  
        self.loginButton.clicked.connect(self.open_login)  

    def register(self):
        username = self.usernameInput.text()
        password = self.passwordInput.text()
        confirm_password = self.confirmPasswordInput.text()
        email = self.emailInput.text()

        if not username or not password or not confirm_password or not email:
            QMessageBox.warning(self, "Cảnh báo", "Vui lòng điền đầy đủ thông tin.")
            return

        if password != confirm_password:
            QMessageBox.warning(self, "Lỗi", "Mật khẩu không khớp.")
            return

        if os.path.exists('credentials.json'):
            with open('credentials.json', 'r') as file:
                credentials = json.load(file)
        else:
            credentials = {}

        if username in credentials:
            QMessageBox.warning(self, "Lỗi", "Tên đăng nhập đã tồn tại.")
            return

        credentials[username] = {
            'password': password,
            'email': email
        }

        with open('credentials.json', 'w') as file:
            json.dump(credentials, file)

        QMessageBox.information(self, "Thành công", "Tạo tài khoản thành công!")
        self.open_login() 

    def open_login(self):
        self.hide()
        self.login_window = LoginWindow() 
        self.login_window.show()  


class MainWindow(QtWidgets.QMainWindow):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("Ứng dụng Đăng nhập - Đăng ký")
        uic.loadUi("Main.ui", self)

        self.manageButton.clicked.connect(self.manage)
        self.settingButton.clicked.connect(self.setting)
        self.logoutButton.clicked.connect(self.login)
        self.galleryButton.clicked.connect(self.gallery)
    
    def manage(self):
        self.hide()
        self.lead_window = LeadWindow()
        self.lead_window.show()

    def setting(self):
        self.hide()
        self.setting_window = SettingWindow()
        self.setting_window.show()

    def login(self):
        self.hide()
        self.login_window = LoginWindow()
        self.login_window.show()

    def gallery(self):
        self.hide()
        self.gallery_window = GalleryWindow()
        self.gallery_window.show()


import unicodedata  

class LeadWindow(QtWidgets.QMainWindow):
    def __init__(self):
        super().__init__()
        uic.loadUi("Lead.ui", self)

        self.homeButton.clicked.connect(self.open_main)
        self.galleryButton.clicked.connect(self.gallery)
        self.settingsButton.clicked.connect(self.setting)
        self.createButton.clicked.connect(self.open_create)
        self.deleteButton.clicked.connect(self.delete)
        self.displayButton.clicked.connect(self.display)
        self.updateButton.clicked.connect(self.open_update)
        self.searchButton.clicked.connect(self.search_student)

        self.list_anime.itemDoubleClicked.connect(self.display) 

        self.load_student_list()

    def open_main(self):
        self.hide()
        self.main_window = MainWindow()
        self.main_window.show()

    def gallery(self):
        self.hide()
        self.gallery_window = GalleryWindow()
        self.gallery_window.show()

    def setting(self):
        self.hide()
        self.setting_window = SettingWindow()
        self.setting_window.show()

    def load_student_list(self):
        """Nạp danh sách tên học sinh từ file JSON"""
        self.list_anime.clear()

        if os.path.exists("students.json"):
            with open("students.json", "r", encoding="utf-8") as file:
                try:
                    students = json.load(file)
                    for student in students:
                        if "name" in student:
                            self.list_anime.addItem(student["name"])  # Chỉ hiển thị tên
                except json.JSONDecodeError:
                    print("Lỗi đọc file JSON!")

    def open_create(self):
        self.create_window = CreateWindow(self)
        self.create_window.show()

    def open_update(self):
        selected_item = self.list_anime.currentItem()
        if selected_item:
            student_name = selected_item.text().strip()
            students = self.load_json_data()

            for student in students:
                if student["name"] == student_name:
                    self.update_window = UpdateWindow(self)
                    self.update_window.show()
                    return
            QMessageBox.warning(self, "Lỗi", "Không tìm thấy dữ liệu học sinh.")
        else:
            QMessageBox.warning(self, "Chưa chọn", "Vui lòng chọn một học sinh để sửa.")

    def load_json_data(self):
        if os.path.exists("students.json"):
            with open("students.json", "r", encoding="utf-8") as file:
                try:
                    return json.load(file)
                except json.JSONDecodeError:
                    return []
        return []

    def write_json_data(self, data):
        with open("students.json", "w", encoding="utf-8") as file:
            json.dump(data, file, ensure_ascii=False, indent=4)

    def remove_accents(self, text):
        """Loại bỏ dấu tiếng Việt"""
        return ''.join(c for c in unicodedata.normalize('NFD', text)
                       if unicodedata.category(c) != 'Mn')

    def search_student(self):
        """Tìm kiếm học sinh theo tên (có hỗ trợ không dấu)"""
        search_text = self.text_search.text().strip().lower()
        self.list_anime.clear()

        if search_text:
            students = self.load_json_data()
            normalized_search = self.remove_accents(search_text.lower())

            for student in students:
                student_name = student["name"]
                normalized_name = self.remove_accents(student_name.lower())

                if normalized_search in normalized_name:
                    self.list_anime.addItem(student_name)
        else:
            self.load_student_list()

    def delete(self):
        selected_items = self.list_anime.selectedItems()
        if not selected_items:
            QMessageBox.warning(self, "Lỗi", "Vui lòng chọn học sinh để xóa")
            return

        students = self.load_json_data()

        for item in selected_items:
            name = item.text().strip()
            students = [s for s in students if s.get("name", "").strip() != name]

        self.write_json_data(students)
        self.load_student_list()
        QMessageBox.information(self, "Thành công", "Xóa học sinh thành công!")

    def display(self):
        """Hiển thị thông tin học sinh"""
        selected_items = self.list_anime.selectedItems()
        if not selected_items:
            QMessageBox.warning(self, "Lỗi", "Vui lòng chọn học sinh để hiển thị")
            return

        students = self.load_json_data()

        for item in selected_items:
            name = item.text().strip()
            results = [s for s in students if s.get("name", "").strip() == name]

            if results:
                student = results[0]
                QMessageBox.information(
                    self, "Thông tin học sinh",
                    f"Tên học sinh: {student['name']}\n"
                    f"Giới tính: {student['gender']}\n"
                    f"Ngày sinh: {student['date']}\n"
                    f"Lớp: {student['group']}\n"
                    f"Số điện thoại: {student.get('phone')}\n"
                    f"Ghi chú: {student.get('note', 'Không có')}"
                )
            else:
                QMessageBox.warning(self, "Lỗi", "Không tìm thấy thông tin học sinh này!")



class CreateWindow(QtWidgets.QDialog):
    def __init__(self, lead_window):
        super().__init__()
        uic.loadUi("CreatHs.ui", self)
        self.lead_window = lead_window  

        self.createButton.clicked.connect(self.add_student)
        self.resetButton.clicked.connect(self.reset_fields)
        self.cancelButton.clicked.connect(self.go_back)

    def add_student(self):
        """Thêm học sinh mới vào danh sách"""
        name = self.nameInput.text().strip()
        date_of_birth = self.dateInput.date().toString("dd/MM/yyyy")
        student_class = self.studentNameInput.text().strip()
        phone = self.phoneInput.text().strip()
        description = self.descInput.toPlainText().strip()

        if not name or not date_of_birth or not student_class or not phone or not description:
            QMessageBox.warning(self, "Cảnh báo", "Vui lòng nhập đầy đủ thông tin.")
            return
        
        new_student = {
            "name": name,
            "date_of_birth": date_of_birth,
            "class": student_class,
            "phone": phone,
            "description": description
        }
        
        students = self.lead_window.load_json_data()  
        students.append(new_student)
        self.lead_window.write_json_data(students)  

        QMessageBox.information(self, "Thành công", "Đã thêm học sinh thành công!")

        self.lead_window.load_animal_list()  
        self.reset_fields()
        self.close()

    def reset_fields(self):
        """Xóa dữ liệu trong các ô nhập"""
        self.nameInput.clear()
        self.dateInput.setDate(self.dateInput.minimumDate())
        self.studentNameInput.clear()
        self.phoneInput.clear()
        self.descInput.clear()

    def go_back(self):
        """Đóng cửa sổ và quay lại màn hình trước"""
        self.close()

class UpdateWindow(QtWidgets.QMainWindow):
    def __init__(self, lead_window):
        super().__init__()
        uic.loadUi("Update.ui", self)
        self.btn_update.clicked.connect(self.update_animal)
        self.btn_reset.clicked.connect(self.reset_fields)
        self.btn_cancel.clicked.connect(self.go_back)

        self.load_animal_list()
        self.combo_animal.currentIndexChanged.connect(self.fill_animal_info)

    def load_animal_list(self):
        """Nạp danh sách động vật vào combobox để chọn trước khi cập nhật"""
        self.combo_animal.clear()
        animals = self.lead_window.load_json_data()
        for animal in animals:
            if "name" in animal:
                self.combo_animal.addItem(animal["name"])

    def fill_animal_info(self):
        """Tự động điền thông tin khi chọn một động vật"""
        selected_name = self.combo_animal.currentText()
        animals = self.lead_window.load_json_data()

        for animal in animals:
            if animal.get("name") == selected_name:
                self.text_creator.setText(animal.get("creator", ""))
                self.date_created.setDate(QtCore.QDate.fromString(animal.get("date", "01/01/2000"), "dd/MM/yyyy"))
                self.text_animal_name.setText(animal.get("name", ""))
                self.combo_group.setCurrentText(animal.get("group", ""))
                self.text_description.setPlainText(animal.get("description", ""))
                break

    def update_animal(self):
        """Cập nhật thông tin động vật"""
        selected_name = self.combo_animal.currentText()
        animals = self.lead_window.load_json_data()

        for animal in animals:
            if animal.get("name") == selected_name:
                # Cập nhật thông tin mới
                animal["creator"] = self.text_creator.text().strip()
                animal["date"] = self.date_created.date().toString("dd/MM/yyyy")
                animal["name"] = self.text_animal_name.text().strip()
                animal["group"] = self.combo_group.currentText().strip()
                animal["description"] = self.text_description.toPlainText().strip()
                break

        self.lead_window.write_json_data(animals)  
        self.lead_window.load_animal_list()  # Cập nhật danh sách trong `LeadWindow`

        QMessageBox.information(self, "Thành công", "Thông tin động vật đã được cập nhật!")

    def reset_fields(self):
        """Xóa dữ liệu trong các ô nhập"""
        self.text_creator.clear()
        self.date_created.setDate(QtCore.QDate.currentDate())
        self.text_animal_name.clear()
        self.combo_group.setCurrentIndex(0)
        self.text_description.clear()

    def go_back(self):
        """Quay lại màn hình trước"""
        self.close()

class SettingWindow(QtWidgets.QMainWindow):
    def __init__(self):
        super().__init__()
        uic.loadUi("Setting.ui", self)
        self.homeButton.clicked.connect(self.open_main)
        self.animalButton.clicked.connect(self.open_lead)
        self.languageComboBox.currentIndexChanged.connect(self.switch_language)
        self.galleryButton.clicked.connect(self.gallery)
        self.languages = {"Tiếng Việt": "vi", "Tiếng Anh": "en"}
        self.load_language()

    def open_main(self):
        self.hide()
        self.main_window = MainWindow()
        self.main_window.show()

    def open_lead(self):
        self.hide()
        self.lead_window = LeadWindow()
        self.lead_window.show()

    def gallery(self):
        self.hide()
        self.gallery_window = GalleryWindow()
        self.gallery_window.show()

    def switch_language(self):
        selected_lang = self.languageComboBox.currentText()
        lang_code = self.languages.get(selected_lang, "vi")
        self.save_language(lang_code)
        self.update_language(lang_code)

    def update_language(self, lang):
        translations = {
        "vi": {
            "home": "Trang Chủ",
            "collection": "Bộ Sưu Tập",
            "lead": "Quản Lý Động Vật",
            "settings": "Cài Đặt",
            "save_settings": "Lưu Cài Đặt",
            "app_settings": "Cài Đặt Ứng Dụng",
            "choose_language": "Chọn Ngôn Ngữ :",
            "dark_mode": "Chế Độ Tối"
        },
        "en": {
            "home": "Home",
            "collection": "Collection",
            "lead": "Animal Management",
            "settings": "Settings",
            "save_settings": "Save Settings",
            "app_settings": "Application Settings",
            "choose_language": "Choose Language:",
            "dark_mode": "Dark Mode"
        }
    }

        t = translations.get(lang, translations["vi"]) 

        self.homeButton.setText(t["home"])
        self.galleryButton.setText(t["collection"])
        self.animalButton.setText(t["lead"])
        self.settingsButton.setText(t["settings"])
        self.saveButton.setText(t["save_settings"])
        self.settingsTitleLabel.setText(t["app_settings"])
        self.languageLabel.setText(t["choose_language"])
        self.darkModeCheckBox.setText(t["dark_mode"])
    def save_language(self, lang):
        with open("config.json", "w") as file:
            json.dump({"language": lang}, file)

    def load_language(self):
        if os.path.exists("config.json"):
            with open("config.json", "r") as file:
                config = json.load(file)
                lang = config.get("language", "vi")
                self.update_language(lang)
                index = list(self.languages.values()).index(lang)
                self.languageComboBox.setCurrentIndex(index)

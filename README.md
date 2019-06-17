# Registration

## Добвить форму RegistrationForm
## В коде авторизации изменить следующий раздел
switch (u.RoleId) {
  case "R": RunnerMenu f = new RunnerMenu(); f.ShowDialog(); break;
  case "C": MessageBox.Show("Вы вошли как Coordinator"); break;
  case "A": MessageBox.Show("Вы вошли как Administrator"); break;
}
## Добавить на форму авторизации кнопку «Регистрация», обработчик нажатия на эту кнопку
RunnerMenu f = new RunnerMenu();
f.ShowDialog();
## Конструктор формы RegistrationForm
public RegistrationForm() {
    InitializeComponent();
    // загрузка списка стран
    DataBaseModel db = new DataBaseModel();
    foreach(var item in db.Country.ToList()) {
        comboBox1.Items.Add(item.CountryCode);
    }
    // Пол
    foreach(var item in db.Gender.ToList()) {
        comboBox2.Items.Add(item.Gender1);
    }
}
// Обработчик нажатия кнопки «Регистрация»
string name = textBox1.Text,
    lName = textBox2.Text,
    email = textBox3.Text,
    pass = textBox4.Text,
    passRepeat = textBox5.Text,
    gender = comboBox2.Text,
    country = comboBox1.Text;

DateTime dateOfBirth = dateTimePicker1.Value;
try {
    if (passRepeat != pass) {
        MessageBox.Show("пароли не совпадают");
        return;
    }
    // здесь дальше проверка пароля, почты и т.д.
    DataBaseModel db = new DataBaseModel();
    // добавление в таблицу user
    db.User.Add(
            new User {
                Email = email,
                FirstName = name,
                LastName = lName,
                Password = pass,
                RoleId = "R"
            }
        );
    //сохранение
    db.SaveChanges();
    // добавление в таблицу runner
    db.Runner.Add(
        new Runner {
            Email = email,
            DateOfBirth = dateOfBirth,
            Gender = gender,
            CountryCode=country
        });
    // сохранение
    db.SaveChanges();
    MessageBox.Show("Вы зарегистрировались!");
}
catch (Exception err) {
    MessageBox.Show(err.Message);
}

# Autorisation

## Добавить в проект новый класс
## Дать имя (DataBaseModel или др.) 
## В появившемся окне выбрать Code First из базы данных
## Нажать на кнопку "Создать соединение"
## Если источник данных выбран не "Microsoft SQL Server", то нажать кнопку "Изменить" и выбрать Microsoft SQL Server
## Чтобы узнать имя сервера откройте Microsoft SQL Server, кликните ПКМ по текущему подключению->Свойства
## Откроется окно, скопируйте имя сервера и вставьте в окно подключения к БД 
## Т.к. мы подключаемся к локальному серверу, оставляем тип аутентификации "Проверка подлинности Windows"
## Откроется окно, нажмите "Далее"
## В открывшемся окне выберите таблицы, с которыми вы будете работать и нажмите "Готово"
``В проект добавятся классы с структурой таблиц.
Добавьте на форму 2 textbox и кнопку
Код на нажатие кнопки:``
## Код 
//проверка на ввод данных
if ((textBox1.Text == "") || (textBox1.Text == "")) {
    MessageBox.Show("Введите логин и пароль");
    return;
}
//try для обработки ошибок
try {
    DataBaseModel db = new DataBaseModel(); //модель БД
    //создание объекта user
    // FirstOrDefault означает поиск по условию, если ничего не нашлось возвращ. null
    User u = db.User.FirstOrDefault(x => x.Email == textBox1.Text && x.Password == textBox2.Text);
    if (u != null) {
        //проверка роли 
        switch (u.RoleId) {
            case "R": MessageBox.Show("Вы вошли как Runner"); break;
            case "C": MessageBox.Show("Вы вошли как Coordinator"); break;
            case "A": MessageBox.Show("Вы вошли как Administrator"); break;
        }
    }
    else {
        MessageBox.Show("Неверный логин/пароль");
        return;
    }
}
catch (Exception err) {
    MessageBox.Show("Произошла ошибка: \n" + err.Message);
}

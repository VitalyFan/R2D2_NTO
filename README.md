
R2D2 Национальная Технологическая Олимпиада 2022
===========



### Состав команды:

- Фан Ин Су (Виталий) - Инженер-программист (Специалист по компьютерному зрению)(Python)
<img src="https://user-images.githubusercontent.com/78021829/161373879-9b8be524-b0d1-4174-970b-c2561c8852f0.jpg" width="200" />

- Кияновский Даниил - Инженер-программист (Python)
<img src="https://user-images.githubusercontent.com/78021829/161373986-e76295b9-93a4-48f6-a650-f78062f9c085.jpg" width="200" />

- Громова Анастасия - Инженер-техник
<img src="https://user-images.githubusercontent.com/78021829/161374019-761c2b24-1140-487f-9302-e5cff962c7c1.PNG" width="200" />


---
## Задача День 1-2 [Код](https://github.com/VitalyFan/R2D2/blob/main/Day1/R2D2_day16.py)
### Созданы необходимые топики с контурами разливов и дефектов /oil_detect и /defect_detect

    oil_pub = rospy.Publisher('/oil_detect', Image) 

    defect_pub = rospy.Publisher('/defect_detect', Image)
***
### Выполнен автономный взлёт:

    navigate_wait(z=0.5, frame_id='body', auto_arm=True)

    rospy.sleep(2)
***
### Получены координаты точки взлёта/посадки (функция get_telemetry)

    telemetry = get_telemetry(frame_id='aruco_map')

    xl = telemetry.x

    yl = telemetry.y
***
### Распознан QR-код, получены и обработаны данные с него (вывод в терминал, получение координат, тип float)

    print(qr)

    qr = qr.split()

    xt = float(qr[0])

    yt = float(qr[1])
***
### Выполнен пролёт к началу трубопровода/к полученным из QR-кода координатам
    navigate_wait(x=xt, y=yt)
***
### Написана функция для пролёта вдоль трубопровода и его мониторинга

    def image_line_callback(data):
    
      cv_image = bridge.imgmsg_to_cv2(data, 'bgr8')  # OpenCV image
    
      img_hsv = cv2.cvtColor(cv_image, cv2.COLOR_BGR2HSV)

      yellow = cv2.inRange(img_hsv, (23,134,186), (90,180,220))

      contours_yll , _ = cv2.findContours(yellow.copy(), cv2.RETR_TREE, cv2.CHAIN_APPROX_SIMPLE)
    
      contours_yll = list(contours_yll)
    
      contours_yll.sort(key=cv2.minAreaRect)

      if len(contours_yll) > 0:
    
          cnt = contours_yll[0]
        
          if cv2.contourArea(cnt) > 150:
        
              rect = cv2.minAreaRect(cnt)
            
              (x_min, y_min), (w_min, h_min), angle = rect
            
              if angle < -45:
            
                  angle = 90 + angle
                
              if w_min < h_min and angle > 0:
            
                  angle = (90 - angle) * -1
                
              if w_min > h_min and angle < 0:
            
                  angle = 90 + angle
                
              center = cv_image.shape[1] / 2
            
              error = x_min - center 

              set_velocity(vx=0.25, vy=error*(-0.01), vz=0, yaw=float('nan'), yaw_rate=angle*(0.008), frame_id='body')

---
## Задача День 3 [Код](https://github.com/VitalyFan/R2D2/blob/main/Day3/R2D2_day23.py)
### Созданы необходимые топики с контурами разливов и дефектов /oil_detect и /defect_detect

    oil_pub = rospy.Publisher('/oil_detect', Image) 

    defect_pub = rospy.Publisher('/defect_detect', Image)
***
### Выполнен автономный взлёт:

    navigate_wait(z=0.5, frame_id='body', auto_arm=True)

    rospy.sleep(1)
***
### Получены координаты точки взлёта/посадки (функция get_telemetry)

    telemetry = get_telemetry(frame_id='aruco_map')

    xl = telemetry.x

    yl = telemetry.y
***
### Распознан QR-код, получены и обработаны данные с него (вывод в терминал, получение координат, тип float)

    print(qr)

    qr = qr.split()

    xt = float(qr[0])

    yt = float(qr[1])
***
### Выполнен пролёт к началу трубопровода/к полученным из QR-кода координатам
    navigate_wait(x = xt, y = yt, z = 0.8)
    rospy.sleep(3)
***
### Написана функция для пролёта вдоль трубопровода и его мониторинга

    def image_line_callback(data):
    
      cv_image = bridge.imgmsg_to_cv2(data, 'bgr8')  # OpenCV image
    
      img_hsv = cv2.cvtColor(cv_image, cv2.COLOR_BGR2HSV)

      yellow = cv2.inRange(img_hsv, (23,134,186), (90,180,220))

      contours_yll , _ = cv2.findContours(yellow.copy(), cv2.RETR_TREE, cv2.CHAIN_APPROX_SIMPLE)
    
      contours_yll = list(contours_yll)
    
      contours_yll.sort(key=cv2.minAreaRect)

      if len(contours_yll) > 0:
    
          cnt = contours_yll[0]
        
          if cv2.contourArea(cnt) > 150:
        
              rect = cv2.minAreaRect(cnt)
            
              (x_min, y_min), (w_min, h_min), angle = rect
            
              if angle < -45:
            
                  angle = 90 + angle
                
              if w_min < h_min and angle > 0:
            
                  angle = (90 - angle) * -1
                
              if w_min > h_min and angle < 0:
            
                  angle = 90 + angle
                
              center = cv_image.shape[1] / 2
            
              error = x_min - center 

              set_velocity(vx=0.25, vy=error*(-0.01), vz=0, yaw=float('nan'), yaw_rate=angle*(0.008), frame_id='body')
              
### Сделан перелёт к точке Старта и посадка на платформу Старта
       navigate_wait(x = xl, y = yl, z = 1.2)
       land_wait()
---
## Задача День 4 [Код](https://github.com/VitalyFan/R2D2/tree/main/Day4) (Изменения кода Дней 1-2 и 3)
### Изменена концепция для следованию по линии(упрощение, но понижение точности и универсальности функции)
    def image_line_callback(data):
        global rev, up_zero, down_zero, a
        cv_image = bridge.imgmsg_to_cv2(data, 'bgr8')  # OpenCV image
        img_hsv = cv2.cvtColor(cv_image, cv2.COLOR_BGR2HSV)
        yellow = cv2.inRange(img_hsv, (25,120,120), (90,255,255))

        up = yellow[0:119, 0:319]
        down = yellow[119:239, 0:319]
        down_zero = np.count_nonzero(down)
        up_zero = np.count_nonzero(up)


        contours_yll , _ = cv2.findContours(yellow.copy(), cv2.RETR_TREE, cv2.CHAIN_APPROX_SIMPLE)
        contours_yll = list(contours_yll)
        contours_yll.sort(key=cv2.minAreaRect)


        if len(contours_yll) > 0:
            cnt = contours_yll[0]

            a = cv2.contourArea(cnt)
Функция понимает в какой части кадра находится начало трубы и в какую сторону она направлена, после чего возвращает данные о местоположении трубы относительно коптера и данные о размере контура трубопровода (a = cv2.contourArea(cnt))
***
### Добавлена обработка новых данных о местоположении озера из QR-кода
    #qr data processing
    xt = float(qr[0])
    yt = float(qr[1])

    xo = float(qr[2])
    yo = float(qr[3])
***
### Полёт в точку сбора воды и её сбор
    #navigation to lake zone
    navigate_wait(x = xo, y = yo, z = 1)
    rospy.sleep(2)

    #water collection
    navigate_wait(x = 0, y = 0, z = -0.5, frame_id='navigate_target')
    rospy.sleep(6)
    print('Successful water withdrawal')
    navigate_wait(x = xo, y = yo, z = 1)
***
### Полёт в точку начала трубы, получение данных о её местоположении и начало мониторинга
    #start of monitoring
    navigate_wait(x = xt, y = yt, z = 0.8)
    rospy.sleep(4)
    image_sub_line = rospy.Subscriber('main_camera/image_raw', Image, image_line_callback, queue_size=1)

    if down_zero > up_zero:
        navigate_wait(x = 0, y = 0, z = 0, yaw=math.radians(-180), frame_id='body')
        rospy.sleep(3)
    while a < 3500 and a > 1000:
        navigate_wait(x = 0.3, z =0, frame_id='navigate_target')
***
### Полёт в точку взлёта/посадки по окончанию мониторинга трубопровода
    #navigation to landing zone and landing
    navigate_wait(x = xl, y = yl)
    land_wait()
***
### Создание финального отчёта
    print('Navigation area: x={}, y={}'.format(xt,yt))
    print('Lake center: x={}, y={}'.format(xo,yo))

***
# Техническое задание 
Руководство по эксплуатации оборудования
---
Руководство служит для понимания работы устройства. 
Специальных навыков для работы не требуется.  
***
Устройство состоит из бутыли, куда набирается вода, крышки и винта. Служит для взятия проб воды и транспортировки. Легок в эксплуатации. Требуется менять винт, если он заржавеет.
***
Нужно прикрутить бутыль к крышке до начала полета и открутить после взятия проб и приземления. 
***
Инструкция по монтажу:
- Прикрепите крышку плоской частью к монтажной деке, закрутите винт M3*8. (Для укрепления стоит воспользоваться клеем момент.кристалл) Шапка винта должна смотреть вниз к полу, если дрон держать в горизонтальном положении. 
- К крышке прикручивается бутыль. Бутыль и крышку нужно напечатать на 3д принтере. Потом надо провести постобработку поддержек. 
- Сосуд следует обработать дихлорэтаном во избежание микротрещин.  
- Хранить в недоступном для детей месте вдали от солнечных лучей. 
- Утилизировать бутыль и крышку можно в контейнер для пластика.
    
<img src=https://user-images.githubusercontent.com/102800060/161370085-ec9ceb49-e6e6-4bed-9825-f595a8aefa70.png width="350" />
Рис.1 Крышка

***

<img src=https://user-images.githubusercontent.com/102800060/161370144-36769156-a7fc-4b1c-bfa2-6122fdf5fad1.png width="350" />
Рис. 2 Бутыль

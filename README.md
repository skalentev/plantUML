@startuml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

skinparam wrapWidth 300
LAYOUT_WITH_LEGEND()
LAYOUT_LANDSCAPE()
title
  <b>FeedbackPublicationArch v2022.05.26</b>
  <i>Система обратной связи: публикация</i>
 end title

 System_Boundary(ok, "Одноклассники") {
   System_Ext(ok_mobile, "Мобильный сайт")
 }

 System_Boundary(vk, "Вконтакте") {
   System_Ext(ok_official_api, "Официальный API")
 }

 Person(operator, "Оператор")
   
 System_Boundary(message_processing_system, "Система обработки сообщений") {
  Container(amo_adapter, "Адаптер AMO CRM", "Python 3.10, FastAPI", "Получение данных для публикации от AMO CRM и постановка публикаций в очередь")
  Container(feedback, "Форма обратной связи", "Python 3.8, Django 3, React", "Формирование ответов на сообщение, постановка ответов в очередь")
  SystemQueue(response_queue, "Kafka", "Ответы для публикации")
  Container(vk_publsher, "VK Publisher", "Python", "Публикация ответов в ВК")
  Container(ok_publsher, "OK Publisher", "Java, Selenium", "Публикация ответов в Одноклассники")
  
  Rel(feedback, response_queue, "Сохраняет", "kafka")
  Rel(vk_publsher, response_queue, "Потребляет", "kafka")
  Rel(ok_publsher, response_queue, "Потребляет", "kafka")
  Rel(operator, feedback, "Отвечает на сообщение", "Внутренний REST API")
 }

 System_Ext(amo, "AMO CRM", "Просмотр заявок и отправка ответов")
 System_Ext(email, "Email")
 Person_Ext(amo_operator, "Оператор AMO CRM")

 Rel(amo_operator, amo, "Отвечает на заявки")
 Rel(amo, email, "Отправка сообщений на Email")
 Rel_U(amo, amo_adapter, "Отправка сообщений в ОК и ВК")

 Rel_L(vk_publsher, ok_official_api, "Публикация", "HTTP")
 Rel_L(ok_publsher, ok_mobile, "Публикация", "HTTP")
 @enduml

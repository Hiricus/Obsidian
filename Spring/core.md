# Компоненты
### XmlBeanDefinitionReader
Сканирует xml с объявлением бинов и переводит его в BeanDefinition-ы. По всей видимости устаревшая штука.
BeanDefinition - мап вида: bean_id : bean_declaration (из какого класса создаётся, все свойства бина, наличие init-метода и т.п.).

### BeanFactory
После того, как все BeanDefinition объекты созданы по ним начинает работать BeanFactory.
Она создаёт объекты по классам, соответствуя BeanDefinition-ам и кладёт их в IoC-контейнер.

### BeanPostProcessor

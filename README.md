# XML Defined Beans in Spring Boot

# 1. Introdução
Antes do Spring 3.0, XML era a única maneira de definir e configurar beans. Spring 3.0 introduziu JavaConfig, permitindo-nos configurar beans usando classes Java. No entanto, os arquivos de configuração XML ainda são usados hoje.

Neste tutorial, discutiremos como integrar configurações XML ao Spring Boot.

# 2. A anotação @ImportResource
A anotação @ImportResource nos permite importar um ou mais recursos contendo definições de bean.

Digamos que temos um arquivo beans.xml com a definição de um bean:

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
    
    <bean class="com.baeldung.springbootxml.Pojo">
        <property name="field" value="sample-value"></property>
    </bean>
</beans>
```

Para usá-lo em um aplicativo Spring Boot, podemos usar a anotação @ImportResource, informando onde encontrar o arquivo de configuração:

```
@Configuration
@ImportResource("classpath:beans.xml")
public class SpringBootXmlApplication implements CommandLineRunner {

    @Autowired 
    private Pojo pojo;

    public static void main(String[] args) {
        SpringApplication.run(SpringBootXmlApplication.class, args);
    }
}
```

Nesse caso, a instância Pojo será injetada com o bean definido em beans.xml.

# 3. Acessando propriedades em configurações XML
Que tal usar propriedades em arquivos de configuração XML? Digamos que queremos usar uma propriedade declarada em nosso arquivo application.properties:

```
sample=string loaded from properties!
```

Vamos atualizar a definição do Pojo, em beans.xml, para incluir a propriedade de amostra:

```
<bean class="com.baeldung.springbootxml.Pojo">
    <property name="field" value="${sample}"></property>
</bean>
```

A seguir, vamos verificar se a propriedade está incluída corretamente:

```
@RunWith(SpringRunner.class)
@SpringBootTest(classes = SpringBootXmlApplication.class)
public class SpringBootXmlApplicationIntegrationTest {

    @Autowired 
    private Pojo pojo;
    @Value("${sample}") 
    private String sample;

    @Test
    public void whenCallingGetter_thenPrintingProperty() {
        assertThat(pojo.getField())
                .isNotBlank()
                .isEqualTo(sample);
    }
}
```

Infelizmente, esse teste falhará porque, por padrão, o arquivo de configuração XML não pode resolver espaços reservados. No entanto, podemos resolver isso incluindo a anotação @EnableAutoConfiguration:

```
@Configuration
@EnableAutoConfiguration
@ImportResource("classpath:beans.xml")
public class SpringBootXmlApplication implements CommandLineRunner {
    // ...
}
```

Essa anotação permite a configuração automática e as tentativas de configurar os beans.

# 4. Abordagem Recomendada
Podemos continuar usando arquivos de configuração XML. Mas também podemos considerar mover todas as configurações para JavaConfig por alguns motivos. Primeiro, configurar os beans em Java é seguro para o tipo, portanto, detectaremos erros de tipo em tempo de compilação. Além disso, a configuração XML pode ficar muito grande, dificultando a manutenção.

# 5. Conclusão
Neste artigo, vimos como usar arquivos de configuração XML para definir nossos beans em um aplicativo Spring Boot.
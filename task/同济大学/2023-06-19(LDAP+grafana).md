1. fix文档：短信接口参数
2. grafana对接LDAP

主要配置说明
host：就是指定你的ldap服务器，可以指定多个，需要分隔符。
port：你的ldap服务器的监听的端口。
bind_dn: 你需要特定ou的管理员账号，我这里使用了域管理者。
bind_password: 上面账号的密码。
search_filter:  用户搜索的过滤表达式，配合search_base_dns。
search_base_dns: 用户搜索的范围，这里在people这个ou里面搜索所有的用户，需要配合search_filter来完成用户的过滤。
group_search_filter: 组搜索的过滤表达式，配合group_search_base_dns

（1） docker-compose.yml 添加以下信息

    grafana:
    image: grafana/grafana:9.4.3 # influxdb:1.8
    container_name: grafana
    volumes:
      - ./grafana/dashboards:/etc/grafana/provisioning/dashboards
      - ./grafana/datasources:/etc/grafana/provisioning/datasources
      - ./grafana/grafana.ini:/etc/grafana/grafana.ini
      - ./grafana/ldap.toml:/etc/grafana/ldap.toml
      - grafana-data:/var/lib/grafana
    ports:
      - "3003:3000"
(2) 在grafana目录下创建ldap.toml文件，添加以下内容

    [log]
    mode = "console"
    filters = "ldap:debug"

    [[servers]]
    host = "202.120.164.30"
    port = 389

    bind_dn = "cn=ruanjian,ou=sa,o=system"
    bind_password = "3edc*UHB4rfv(IJN"

    use_ssl = false
    start_tls = false
    ssl_skip_verify = false

    search_base_dns = ["ou=people,dc=tongji"]
    search_filter = "(uid=%s)"

    [[servers.group_mappings]]
    group_dn = "cn=graduate,ou=usergroups,dc=tongji"
    org_role = "Viewer"

    [[servers.group_mappings]]
    group_dn = "cn=staff,ou=usergroups,dc=tongji"
    org_role = "Editor"

    [servers.attributes]
    name = "givenName"
    surname = "sn"
    username = "cn"
    email = "mail"
    member_of = "groupMembership"

 （3）grafana.ini 添加以下信息

    [auth.ldap]
    allow_sign_up = true
    config_file = /etc/grafana/ldap.toml
    enabled = true
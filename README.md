# ansible-playbook
ansible-book# Ansible安装与应用

### 简介：

Ansible是一个开源配置管理工具，可以使用它来自动化任务，部署应用程序实现IT基础架构。Ansible可以用来自动化日常任务，比如，服务器的初始化配置、安全基线配置、更新和打补丁系统，安装软件包等。Ansible架构相对比较简单，仅需通过SSH连接客户机执行任务即可。

Ansible使用过程中会用到一些概念术语，

Ansible的与节点有关的重要术语包括控制节点，受管节点，清单和主机文件：

控制节点（Control node）：指安装了Ansible的主机，也叫Ansible服务器端,管理机。 Ansible控制节点主要用于发布运行任务，执行控制命令。Ansible的程序都安装在控制节点上，控制节点需要安装Python和Ansible所需的各种依赖库。注意：目前Ansible还不能安装在Windows下。

受控节点（Managed nodes）：也叫客户机，就是想用Ansible执行任务的客户服务器。

清单（Inventory）：受控节点的列表，就是所有要管理的主机列表。

host文件：清单列表通常保存在一个名为host文件中。在host文件中，可以使用IP地址或者主机名来表示具体的管理主机和认证信息，并可以根据主机的用户进行分组。缺省文件：/etc/ansible/hosts，可以通过-i指定自定义的host文件。

模块（Modules）：模块是Ansible执行特定任务的代码块。比如：添加用户，上传文件和对客户机执行ping操作等。Ansible现在默认自带450多个模块，，Ansible Galaxy公共存储库则包含大约1600个模块。

任务（Task）：是Ansible客户机上执行的操作。可以使用ad-hoc单行命令执行一个任务。

剧本(Playbook):是利用YAML标记语言编写的可重复执行的任务的列表，playbook实现任务的更便捷的读写和贡献。比如，在Github上有大量的Ansible playbooks共享，你要你有一双善于发现的眼睛你就能找到大量的宝藏。

角色（角色）：角色是Ansible 1.2版本约会的新特性，用于层次性，结构化地组织剧本。角色能够根据层次结构自动加载变量文件，任务以及处理程序等。
### 一、安装篇

1、安装 epel-release 源，能够使用到更新版的ansible

```
[root@k8s-master1 ~]# yum -y install epel-release
```

2、安装ansible

```
[root@k8s-master1 ~]# yum -y install ansible
```

这个时候我们就安装完毕了，简单吧，其实就是这么简单

3、验证安装是否成功

```
 [root@k8s-master1 ~]# rpm -qa | grep ansible
ansible-2.9.10-1.el7.noarch
```

4、或者在命令行中打出前四个字母然后 tab 可以自动补全即安装成功。

```
[root@k8s-master1 ~]# ansible
usage: ansible [-h] [--version] [-v] [-b] [--become-method BECOME_METHOD]
               [--become-user BECOME_USER] [-K] [-i INVENTORY] [--list-hosts]
               [-l SUBSET] [-P POLL_INTERVAL] [-B SECONDS] [-o] [-t TREE] [-k]
               [--private-key PRIVATE_KEY_FILE] [-u REMOTE_USER]
               [-c CONNECTION] [-T TIMEOUT]
               [--ssh-common-args SSH_COMMON_ARGS]
               [--sftp-extra-args SFTP_EXTRA_ARGS]
               [--scp-extra-args SCP_EXTRA_ARGS]
               [--ssh-extra-args SSH_EXTRA_ARGS] [-C] [--syntax-check] [-D]
               [-e EXTRA_VARS] [--vault-id VAULT_IDS]
               [--ask-vault-pass | --vault-password-file VAULT_PASSWORD_FILES]
               [-f FORKS] [-M MODULE_PATH] [--playbook-dir BASEDIR]
               [-a MODULE_ARGS] [-m MODULE_NAME]
               pattern
ansible: error: too few arguments
```

5、Ansible配置

默认的配置文件位于/etc/ansible/ansible.cfg下。可以使用此配置文件来修改绝Ansible大多数设置，一般无需额外多配置，默认配置应能满足大多数使用情况。关于Ansible配置文件，其执行程序会按照一定顺序搜索配置：

Ansible按照以下顺序搜索配置文件，优先配置优先使用，而忽略其余配置文件：

$ANSIBLE_CONFIG环境变量。

任务当前目录下的：ansible.cfg（如果在当前目录中）。

当前用户下的ansible.cfg：~/.ansible.cfg

默认配置文件：/etc/ansible/ansible.cfg。

### 二、使用篇

提示：如果使用ansible对我们的主机进行操作的话，需要满足以下条件

- 是否拥有相应的模块（随着ansible的安装已经安装好了，有其他需求可后期自行增加）
- 是否拥有主机清单（1、可以为ansible 读取主机列表，2、通过主机清单来进行主机分组）



#### 1、主机清单配置

主机清单位置：

```
[root@k8s-master1 ~]# cat /etc/ansible/hosts
```

##### 配置主机清单方法 1

直接在hosts中写入主机名或者ip地址

##### 配置主机清单方法 2

在主机清单文件中添加分组，把主机名或者ip 写入分组即可

```
[k8s-group]
192.168.38.127
192.168.38.128

```

#### 2、Ansible ad-hoc单行命令执行

ad-hoc命令行是我们可以随手执行的单个ansible任务，是ansible任务快速执行方式。对于一些快速获取的任务使用ad-doc命令非常简便有效，而且有助于我们学习和熟悉Ansible的使用

常用的Ansible命令行选项如下：

-b，--become：特权方式运行命令。

-m：要使用的模块名称。

-a，--args：制定模块所需的参数。

-u：制定连接的用户名。

-h，--help显示帮助内容。

-v，--verbose以详细信息模式运行命令，可以用来调试错误。

使用ping模块检查与客户机的连接性，请执行以下操作：

ansible all -m ping

在上面的命令中，all指定Ansible应该在所有主机上运行此命令，也可以按照分组比如nodes 或者主机 node1等。执行结果如下：




可见node1是通的，而node2和node4不通。



#### 3、使用ad-hoc命令管理软件包



使用Ansible的ad-hoc命令，可以给客户端安装软件包。我们只需执行一个单行命令，然后实现安装。

比如：在分组客户机安装Apache只需要执行：

```
ansible web -m yum yum -a "name=httpd state=present" -b
```



上面我们给web组的客户机批量安装了Apache服务器，下面我们来看怎么启动httpd服务。

启动httpd服务只需要执行：

```
ansible web -b -m service -a "name=httpd enabled=yes"
```





#### 4、Ansible Playbook

Playbook 是Ansible提供的最强大的任务执行方法。与ad-hoc命令不同，Playbooks配置在文件中，可以重用和共享给其他人。

playbooks是以YAML标记语言来定义的。每个playbook由一个或多个play组成。play的目标是将一组主机映射到任务中去。每个play包含一个或多个任务，这些任务每次执行一次。

下面是一个简单的Ansible httpd.yaml playbook，用于给web组上安装Apache服务器：

```
---
- hosts: web
remote_user: ansible
tasks:
- name: Ensure apache is installed and updated
yum:
name: httpd
state: latest
become: yes
```



执行playbook使用ansible-playbook命令，格式如下：

ansible-playbook -i <hostfile> <playbook.yaml>

ansible-playbook httpd.yaml结果：

---

---
- hosts: web
remote_user: ansible
become: yes
tasks:
- name: Installing apache
yum:
name: httpd
state: latest
- name: Enabling httpd service
service:
name: httpd
enabled: yes
notify:
- name: restart httpd
handlers:
- name: restart httpd
service:
name: httpd
state: restarted
- hosts: all
remote_user: ansible
become: yes
tasks:
- name: Installing git
yum:
name: git
state: latest

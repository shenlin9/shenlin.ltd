title: Composer - Chapter 9 - 插件

categories:
  - PHP
  - Composer
tags:
  - PHP
  - Composer

---

有修改或扩展 Composer 功能的需要时，可以使用插件。

<!--more-->

## 创建插件

### 创建插件包

Composer 的插件就是一个 Composer 包，有些儿特殊：
* `type` 属性必须是 `composer-plugin`
* `extra` 属性必须包含元素 `class`，它的值是插件的命名空间和类名，如果包里有多个插件，则应是一个类名的数组
* 必须 `require` 一个特殊的包 `composer-plugin-api` 来定义你的插件是和哪个版本的插件 API 兼容

```
{
    "name": "my/plugin-package",
    "type": "composer-plugin",
    "require": {
        "composer-plugin-api": "^1.1"
    },
    "extra": {
        "class": "My\\Plugin"
    }
}
```

### 创建插件类

每个插件都必须提供一个实现了接口`Composer\Plugin\PluginInterface`的类
```
<?php

namespace phpDocumentor\Composer;

use Composer\Composer;
use Composer\IO\IOInterface;
use Composer\Plugin\PluginInterface;

class TemplateInstallerPlugin implements PluginInterface
{
    public function activate(Composer $composer, IOInterface $io)
    {
        $installer = new TemplateInstaller($io, $composer);
        $composer->getInstallationManager()->addInstaller($installer);
    }
}
```
* 类中的`active()`方法在插件加载之后执行
* `active()`的两个参数分别是`Composer\Composer`类和`Composer\IO\IOInterface`的实例
* 使用这两个对象实例可以读取所有设置参数和操作内部对象和状态。

## 事件处理

插件也可以通过实现接口 `Composer\EventDispatcher\EventSubscriberInterface` 来实现自动事件处理

通过实现 `getSubscribedEvents()` 向事件注册针对的处理方法，注册的方法必须返回一个数组，键名为事件名，键值为方法名
```
public static function getSubscribedEvents()
{
    return array(
        'post-autoload-dump' => 'methodToBeCalled',
        // ^ event name ^         ^ method name ^
    );
}
```

上面的键值还可以为一个数组，多传一个优先级参数，数字越大优先级越高越早被调用，默认为 0
```
public static function getSubscribedEvents()
{
    return array(
        // Will be called before events with priority 0
        'post-autoload-dump' => array('methodToBeCalled', 1)
    );
}
```

键值还可以为多维数组来绑定多个方法
```
public static function getSubscribedEvents()
{
    return array(
        'post-autoload-dump' => array(
            array('methodToBeCalled'      ), // Priority defaults to 0
            array('someOtherMethodName', 1), // This fires first
        )
    );
}
```

完整例子
```
<?php

namespace Naderman\Composer\AWS;

use Composer\Composer;
use Composer\EventDispatcher\EventSubscriberInterface;
use Composer\IO\IOInterface;
use Composer\Plugin\PluginInterface;
use Composer\Plugin\PluginEvents;
use Composer\Plugin\PreFileDownloadEvent;

class AwsPlugin implements PluginInterface, EventSubscriberInterface
{
    protected $composer;
    protected $io;

    public function activate(Composer $composer, IOInterface $io)
    {
        $this->composer = $composer;
        $this->io = $io;
    }

    public static function getSubscribedEvents()
    {
        return array(
            PluginEvents::PRE_FILE_DOWNLOAD => array(
                array('onPreFileDownload', 0)
            ),
        );
    }

    public function onPreFileDownload(PreFileDownloadEvent $event)
    {
        $protocol = parse_url($event->getProcessedUrl(), PHP_URL_SCHEME);

        if ($protocol === 's3') {
            $awsClient = new AwsClient($this->io, $this->composer->getConfig());
            $s3RemoteFilesystem = new S3RemoteFilesystem($this->io, $event->getRemoteFilesystem()->getOptions(), $awsClient);
            $event->setRemoteFilesystem($s3RemoteFilesystem);
        }
    }
}
```

## 插件功能

Composer 定义了一组可以由插件实现的标准功能，目的是使插件生态更稳定，因此减少了对类`composer/composer`内部状态的干扰，为公共的插件需求提供显式的扩展点。

功能性插件必须实现接口`Composer\Plugin\Capable`，并且在方法`getCapabilities()`中声明功能，方法必须返回一个数组，键名为 Composer 定义的功能类名，键值为实现功能的插件类名

```
<?php

namespace My\Composer;

use Composer\Composer;
use Composer\IO\IOInterface;
use Composer\Plugin\PluginInterface;
use Composer\Plugin\Capable;

class Plugin implements PluginInterface, Capable
{
    public function activate(Composer $composer, IOInterface $io)
    {
    }

    public function getCapabilities()
    {
        return array(
            'Composer\Plugin\Capability\CommandProvider' => 'My\Composer\CommandProvider',
        );
    }
}
```

`Composer\Plugin\Capability\CommandProvider` 允许为 Composer 注册命令
```
<?php

namespace My\Composer;

use Composer\Plugin\Capability\CommandProvider as CommandProviderCapability;
use Symfony\Component\Console\Input\InputInterface;
use Symfony\Component\Console\Output\OutputInterface;
use Composer\Command\BaseCommand;

class CommandProvider implements CommandProviderCapability
{
    public function getCommands()
    {
        return array(new Command);
    }
}

class Command extends BaseCommand
{
    protected function configure()
    {
        $this->setName('custom-plugin-command');
    }

    protected function execute(InputInterface $input, OutputInterface $output)
    {
        $output->writeln('Executing');
    }
}
```
* `custom-plugin-command` 现在可以使用 Composer 运行了


## 使用插件

在运行`composer install`命令安装完毕插件之后即自动加载，

在 composer 启动过程中发现已安装软件包中有插件时也会自动加载。

使用 `composer global` 命令安装在 `COMPOSER_HOME` 目录中的插件会在当前项目插件之前加载。

使用 `--no-plugins` 选项可以不加载已安装的插件，当有插件出错你想更新或卸载它时特别有用。

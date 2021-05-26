#### Contents
*   <a href="#syn">Synopsis</a>
*   <a href="#install">Install the sample module</a>
*   <a href="#add-register">Add and register the command</a>
*   <a href="#contrib">Contributors</a>
*   <a href="#lic">License</a>


<h2 id="syn">Synopsis</h2>

This sample module creates two new command-line commands:

*   `check-active` (displays the list of enabled modules)
*   `greeting` (displays a greeting)

You can use this sample module as an example to create your own custom commands.

<h2 id="install">Install the sample module</h2>
You'll find it useful to install this sample module so you can refer to it when you're coding your own custom commands. If you'd prefer not to, continue with <a href="#add-register">Add and register the command</a>.

**Note**: Following is one way to install the sample modules. With the release of Magento 2.0, you'll also be able to install modules using the Magento Marketplaces.

### Copy the code
Create a directory for the sample module and copy `cutom-cli-magento` to it:

    mkdir -p <your Magento install dir>/app/code/Magento/CommandExample
    cp -R <cutom-cli-magento clone dir>/bytology/* <your Magento install dir>/app/code/Magento/CommandExample

### Update the Magento database and schema
If you added the module to an existing Magento installation, run the following command:

    php <your Magento install dir>/bin/magento setup:upgrade

### Verify the module is installed
Enter the following command:

    php <your Magento install dir>/bin/magento --list

The following confirms you installed the module correctly:

    example
        check-active              Checks application status (installed or not)
        greeting                  Greeting command

<h2 id="add-register">Add and register the command</h2>
To add the command and register it:

1.  Create a Command class (the recommended location is `<module_dir>/Console/Command`).

    This class represents each of your module's CLI commands. (See `app/code/Magento/CommandExample/Console/Command` for examples.)

    Your Command class extends base class (`Symfony\Component\Console\Command\Command`) and overrides two protected methods:

        class CheckActiveModulesCommand extends Command
        {
            /**
             * Module list
             *
             * @var ModuleListInterface
             */
            private $moduleList;

            /**
             * @param ModuleListInterface $moduleList
             */
            public function __construct(ModuleListInterface $moduleList)
            {
                $this->moduleList = $moduleList;
                parent::__construct();
            }

            /**
             * {@inheritdoc}
             */
            protected function configure()
            {
                $this->setName('example:modules:check-active')
                    ->setDescription('Checks application status (installed or not)');

                parent::configure();
            }

            /**
             * {@inheritdoc}
             */
            protected function execute(InputInterface $input, OutputInterface $output)
            {
                $output->writeln('<info>List of active modules:<info>');
                foreach ($this->moduleList->getNames() as $moduleName) {
                    $output->writeln('<info>' . $moduleName . '<info>');
                }
            }
        }

2.  Declare your Command class in `Magento\Framework\Console\CommandList` using dependency injection (`<module_dir>/etc/di.xml`):

        <type name="Magento\Framework\Console\CommandList">
            <arguments>
                <argument name="commands" xsi:type="array">
                    <item name="check_active_status_command" xsi:type="object">Magento\CommandExample\Console\Command\CheckActiveModulesCommand</item>
                </argument>
            </arguments>
        </type>

2.  Clean the cache and compiled code directories:

        cd <your Magento install dir>/var
        rm -rf cache/* page_cache/* di/* generation/* 

<h2 id="contrib">Contributors</h2>

Magento Core team

<h2 id="lic">License</h2>

[Open Source License](LICENSE.txt)

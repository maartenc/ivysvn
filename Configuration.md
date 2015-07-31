Configuring IvySvn is explained below.

# Configuration #

Note: All these examples reflect the IvySvn 2.1.0 release, for upgrading from 2.0.0 see the section "Upgrading from 2.0.0 to 2.1.0" on the [Installation](Installation.md) wiki page for important configuration changes.

1. Create an `ivysettings.xml` file along the lines of the example file shown below. This example includes **all** possible values which can be set on the IvySvn Resolver for reference purposes only. For real world use refer to the table below and only set the attributes you require, replacing the values which are all uppercase with values that suit your setup.

```
<ivysettings>

  <typedef name="svn" classname="fm.last.ivy.plugins.svnresolver.SvnResolver"/>

  <resolvers>
    <svn name="ivysvn" 
         userName="${svn.user.name}" 
         userPassword="${svn.user.password}" 
         repositoryRoot="PROTOCOL://HOSTNAME/PATH/TO/IVY/REPOSITORY/ROOT"
         keyFile="${ssh.keyfile}" 
         sshPort="${ssh.port}" 
         sshPassphrase="${ssh.keyfile.passphrase}" 
         certFile="${ssl.certfile}"
         sslPassphrase="${ssl.passphrase}" 
         storageAllowed="${svn.storage.allowed}"
         binaryDiff="${svn.binarydiff}"
         binaryDiffFolderName="${svn.binarydiff.folder}"
         retrieveRevision="${svn.retrieve.revision}"
         cleanupPublishFolder="${svn.cleanup.publish.folder}">
      <ivy pattern="[organisation]/[module]/[revision]/ivy.xml"/>
      <artifact pattern="[organisation]/[module]/[revision]/[artifact].[ext]"/>
    </svn>
  </resolvers>

  <settings defaultResolver="ivysvn" />
    <modules>
      <module organisation="YOUR_ORGANISATION" name="ivysvnresolver" resolver="ivysvn"/>
    </modules>
</ivysettings>
```

For more information on the values in `ivysettings.xml` refer to the [Ivy documentation](http://ant.apache.org/ivy/history/trunk/configuration.html). Make sure that this file is included in your Ant build file before you call any Ivy ant targets like ivy:retrieve, ivy:publish etc. You include it with a line like so:
```
<ivy:settings file="ivysettings.xml" />
```

2. Remove, add and set attributes of the "svn" element to suit your environment as per the table below (we recommend setting them via properties in your Ant build file) as shown in the example above.

| **Attribute** | **Description** | **Required** |
|:--------------|:----------------|:-------------|
| name          | Name of the resolver. | Always.      |
| userName      | Subversion user name. | Always.      |
| userPassword  | Subversion password. | Only for password authentication. |
| repositoryRoot | Ivy repository root location (including protocol, host and Subversion root path). | Always.      |
| keyFile       | Path to SSH key file. | Only for svn+ssh:// connections that require username and keyfile authentication. |
| sshPassphrase | The SSH key file passphrase (if any). | Only for svn+ssh:// connections that require username and keyfile authentication with a keyfile requiring a passphrase, defaults to "". |
| sshPort       | The SSH port.   | Only for svn+ssh:// connections where the SSH port is not the default of 22. |
| certFile      | Path to client side certificate file. | Only for https:// connections that require client side certificates for authentication. |
| sslPassphrase | The SSL/Client certificate passphrase. | Only for https:// connections that require client side certificates with their own passphrase. |
| storageAllowed | Whether to store credentials in the global authentication cache or not. | No, defaults to "false". |
| binaryDiff    | Whether to publish to an intermediate folder to take advantage of Subversion's binary diff mechanism. | No, defaults to "true". |
| binaryDiffFolderName | Name of the intermediate folder to publish to if binaryDiff is set to "true". | No, defaults to "LATEST". |
| retrieveRevision | Subversion revision to use for retrieve operations. If set, the Ivy cache _must_ be cleaned out every time its value is changed. | No, defaults to -1 (equivalent to "HEAD"). |
| cleanupPublishFolder | Whether to delete the current contents of folder being published to as part of publish operation. | No, if binaryDiff="true" the folder being published to is _always_ deleted, in which case this property is ignored and cannot be set to "true" (this will result in a build error). If binaryDiff="false" then this property defaults to "false". |

So an example ivysettings.xml for a Subversion repository accessed over SSH on the default SSH port using SSH username and password authentication would be:
```
<ivysettings>

  <typedef name="svn" classname="fm.last.ivy.plugins.svnresolver.SvnResolver"/>

  <resolvers>
    <svn name="ivysvn" repositoryRoot="svn+ssh://example.com/svn/ivyrep" userName="USER" userPassword="PASSWORD">
      <ivy pattern="[organisation]/[module]/[revision]/ivy.xml"/>
      <artifact pattern="[organisation]/[module]/[revision]/[artifact].[ext]"/>
    </svn>
  </resolvers>

  <settings defaultResolver="ivysvn" />
    <modules>
      <module organisation="some_organisation" name="ivysvnresolver" resolver="ivysvn"/>
    </modules>
</ivysettings>  
```
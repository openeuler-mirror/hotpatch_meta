# hotpatch_meta

#### 介绍
本仓库实现自动制作热补丁，提交pr即可自动触发热补丁制作流程

#### 开发手动提交pr指导
- 首先提交pr前，需要先手动创建热补丁issue，热补丁issue用来跟踪整个热补丁制作流程以及后续热补丁发布

热补丁issue模板：
```text
标题：[hotpatch]/[软件-版本号]fix issue_id

类型选择：hotpatch

描述：  问题类别：cve/bugfix/feature
热补丁元数据：https://gitee.com/openeuler/hotpatch_meta/blob/master/{branch}/{repo}/{version}/hotmetadata_{ACC/SGL}.xml

```
- 提交pr还需要patch和元数据文件hotmetadata_ACC.xml/hotmetadata_SGL.xml 
  - patch文件是本次需要做热补丁的补丁文件，在本仓库路径为：{branch}/{repo}/{version}/patch/
  - 元数据文件hotmetadata_ACC.xml/hotmetadata_SGL.xml，ACC代表制作增量补丁，SGL代表制作单独补丁，根据需求修改对应的元数据文件
  - 元数据文件hotmetadata_ACC.xml/hotmetadata_SGL.xml中记录了本次制作所需要的详细信息，格式示例参考下面，在本仓库路径为：{branch}/{repo}/{version}/hotmetadata.xml 
 ```text
branch：基于哪个分支源码做热补丁
repo：需要制作热补丁的源码仓库
version：基于哪个版本的源码制作热补丁
 ```

hotmetadata_ACC.xml格式示例：
```xml
<?xml version="1.0" ?>
<ns0:hotpatchdoc xmlns:ns0="https://gitee.com/openeuler/HotPatch_metadata">
	<DocumentTitle xml:lang="en">Managing Hot Patch Metadata</DocumentTitle>
	<HotPatchList>
		<Package name="源码仓库-版本号">
			<hotpatch version="1" release="1" type="修复问题类型[cve/bugfix/feature]" inherit="0" status="unconfirmed">
				<SRC_RPM>源码包下载路径（需要reealse正式路径）</SRC_RPM>
				<Debug_RPM_X86_64>x86_64架构debuginfo包下载路径（需要reealse正式路径）</Debug_RPM_X86_64>
				<Debug_RPM_Aarch64>aarch64架构debuginfo包下载路径（需要reealse正式路径）</Debug_RPM_Aarch64>
				<patch>本次需要制作热补丁的patch包名1</patch>
				<patch>本次需要制作热补丁的patch包名2</patch>
				<patch>...</patch>
				<issue id="cve编号/issue_id" hotpatch_issue_href="需要解决的issue链接"/>
				<issue id="cve编号2/issue_id2" hotpatch_issue_href="需要解决的issue链接2"/>
				<issue.../>
				<hotpatch_issue_link>热补丁issue链接（需要开发手动创建）</hotpatch_issue_link>
			</hotpatch>
		</Package>
	</HotPatchList>
</ns0:hotpatchdoc>
```
 - 如果需要基于上个版本制作新版本热补丁，请确认上个版本热补丁已发布，并且其相应status字段为"confirmed";
 - 如果对应路径下存在热补丁元数据，需要复制hotpatch代码块，version-release需要比上个版本的大，其它字段修改为本次制作热补丁需要的值;
 - 如果对应路径不存在热补丁元数据，复制上面hotpatch代码块，其它字段修改为本次制作热补丁需要的值;
 - 支持修复多个issue问题;
 - 支持多个patch包，patch包需要按照顺序写入

hotmetadata_SGL.xml格式示例：
```xml
<?xml version="1.0" ?>
<ns0:hotpatchdoc xmlns:ns0="https://gitee.com/openeuler/HotPatch_metadata">
	<DocumentTitle xml:lang="en">Managing Hot Patch Metadata</DocumentTitle>
	<HotPatchList>
		<Package name="源码仓库-版本号">
			<hotpatch name="SGL-issue1-issue2" version="1" release="1" type="修复问题类型[cve/bugfix/feature]" inherit="0" status="unconfirmed">
				<SRC_RPM>源码包下载路径（需要reealse正式路径）</SRC_RPM>
				<Debug_RPM_X86_64>x86_64架构debuginfo包下载路径（需要reealse正式路径）</Debug_RPM_X86_64>
				<Debug_RPM_Aarch64>aarch64架构debuginfo包下载路径（需要reealse正式路径）</Debug_RPM_Aarch64>
				<patch>本次需要制作热补丁的patch包名1</patch>
				<patch>本次需要制作热补丁的patch包名2</patch>
				<patch>...</patch>
				<issue id="cve编号/issue_id" hotpatch_issue_href="需要解决的issue链接"/>
				<issue id="cve编号2/issue_id2" hotpatch_issue_href="需要解决的issue链接2"/>
				<issue.../>
				<hotpatch_issue_link>热补丁issue链接（需要开发手动创建）</hotpatch_issue_link>
			</hotpatch>
		</Package>
	</HotPatchList>
</ns0:hotpatchdoc>
```
 - hotpatch name字段是SGL-issue1-issue2，此处需要和下面的issue id字段一致，多个issue以“-”隔开;
 - 如果对应路径下存在热补丁元数据，需要复制hotpatch代码块，新增patch时，version-release需要比hotpatch name字段相同的上个版本的大，其它字段修改为本次制作热补丁需要的值;
 - 如果对应路径不存在热补丁元数据，复制上面hotpatch代码块，其它字段修改为本次制作热补丁需要的值;
 - 支持修复多个issue问题;
 - 支持多个patch包，patch包需要按照顺序写入

推送hotmetadata_ACC.xml/hotmetadata_SGL.xml和patch文件后，提交pr至该仓库，即可触发热补丁制作流程，需要重试的话评论/retest，会重新触发热补丁制作流程
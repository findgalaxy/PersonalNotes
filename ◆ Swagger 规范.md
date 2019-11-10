◆ Swagger 规范

➢ 编写良好的 Swagger 描述信息有助于前后端API联调测试

➢ @Api(tags = SwaggerApiConfig.TASK)：标注在 Controller 类上，描述类信息

➢ @ApiOperation(value = “xxx”)：标注在 Controller 接口方法上，描述API层级、描述信息、访问控制等

➢ @ApiModel(“xxx”)：标注在实体类上，描述实体信息

➢ @ApiModelProperty(“xxx”)：标注在实体字段上，描述字段信息

➢ @ApiImplicitParams()：标注在 Controller 接口方法上，对接口参数进行说明2

➢ @ApiParam(value = “xxx”)：标注在 Controller 接口方法参数上，对参数进行说明

-------------------------------------------------------------------------------------------------------@ApiModel(***\*"用户信息"\****) **// Swagger 实体描述**

@ModifyAudit **//在类上使用，启用审计字段支持，实体类加上该注解后，插入和更新会启动对 lastUpdateDate、lastUpdatedBy 自维护字段支持**

@VersionAudit **//在类上使用，启用objectVersionNumber自维护支持，插入一条数据objectVersionNumber默认为1，每次update后objectVersionNumber自增1q**

@Table(name = ***\*"todo_user"\****) **// 表映射**

@JsonInclude(JsonInclude.Include.***\**NON_NULL\**\***) **// 数据返回前端时，排除为空的字段**

**AuditDomain包含5个自维护字段，使用@ModifyAudit和@VersionAudit的实体类要继承该类**

@Id **// 主键主键，注意是 javax.persistence.Id**

@GeneratedValue **//对于自增张、序列（SEQUENCE）类型的主键，需要添加该注解GeneratedValue**

@Length(max = 30) **// 长度控制**

@NotBlank **// 非空控制**

@Pattern(regexp = Regexs.***\**CODE\**\***, message = ***\*"员工编号格式不正确"\****) **// 格式控制**

@Pattern(regexp = Regexs.***\**EMAIL\**\***, message = ***\*"邮箱格式不正确"\****)
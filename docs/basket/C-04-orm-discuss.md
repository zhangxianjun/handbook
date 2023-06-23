## 讨论
### 操作数据库的库
#### tk.mybatis 和 mybatis-plus ？
#####  SQL语句
* class

```java
@Select("" +
            "  select a.token_id,a.available_balance,a.frozen_balance,b.name,b.display_precision" +
            "  FROM nft_user_balance a INNER JOIN nft_token b" +
            "  ON a.token_id=b.symbol" +
            "  WHERE a.user_id=#{userId}"
            + "")
    @Results({
            @Result(column = "token_id", property = "tokenId"),
            @Result(column = "available_balance", property = "availableBalance"),
            @Result(column = "frozen_balance", property = "frozenBalance"),
            @Result(column = "display_precision", property = "displayPrecision"),
            @Result(column = "name", property = "tokenName"),
    })
    List<UserBalancePo> listNftUserBalance(@Param("userId") long userId);
```

* xml

```xml
<select id="selectBalanceFlow" parameterType="spark.nft.reconciliation.dto.BalanceFlowParam" resultType="spark.nft.reconciliation.dto.BalanceFlow">
        select user_id as userId ,token_id as tokenId, SUM(amount) as totalAmount from nft_user_balance_flow
        <where>
            <if test="flowParam.userId != null">
                user_id = #{flowParam.userId,jdbcType=BIGINT}
            </if>
            <if test="flowParam.tokenId != null">
                and token_id = #{flowParam.tokenId,jdbcType=VARCHAR}
            </if>
            <if test="flowParam.beginFlowId != null">
                and id <![CDATA[ > ]]> #{flowParam.beginFlowId,jdbcType=BIGINT}
            </if>
            <if test="flowParam.endFlowId != null">
                and id <![CDATA[ <= ]]> #{flowParam.endFlowId,jdbcType=BIGINT}
            </if>
            <if test="flowParam.flowTypes !=null and flowParam.flowTypes.size()!=0">
                <foreach item="item" index="index" collection="flowParam.flowTypes"
                         open="and type in (" separator="," close=")" nullable="true">
                    #{item}
                </foreach>
            </if>
        </where>
    </select>
```

##### orm操作
```java
public Page<NftCollectionPo> selectNftCollectionOfBlindBoxList(long userId, int pageNo, int pageSize) {
        Example example = new Example(NftCollectionPo.class);
        example.createCriteria().andEqualTo("userId", userId).andEqualTo("isBlindBox", 1);
        example.orderBy("updatedTime").desc();
        Page<NftCollectionPo> page = PageHelper.startPage(pageNo, pageSize);
        nftCollectionPoMapper.selectByExample(example);
        return page;
    }
```
```java
public MessageCenterResp getMessageDetail(Long messageId) {
        MessageCenterResp messageCenterResp = new MessageCenterResp();
        PlatformAnnouncement platformAnnouncement = platformAnnouncementMapper.selectOne(
                Wrappers.<PlatformAnnouncement>lambdaQuery()
        .eq(PlatformAnnouncement::getId, messageId)
        .eq(PlatformAnnouncement::getLanguageTag,LanguageUtils.getLanguageTag())
        );
        
        if(!ObjectUtil.isNull(platformAnnouncement)){
            messageCenterResp.setMsgId(platformAnnouncement.getId());
            messageCenterResp.setTitle(platformAnnouncement.getTitle());
            messageCenterResp.setContent(platformAnnouncement.getContent());
            messageCenterResp.setUrl(Strings.isBlank(platformAnnouncement.getUrl())?"":appConfig.getS3Url() + platformAnnouncement.getUrl());
            messageCenterResp.setPublishTime(platformAnnouncement.getPublishTime());
        }
        return messageCenterResp;
    }
```


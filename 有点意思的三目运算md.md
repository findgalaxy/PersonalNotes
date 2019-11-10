
@Transactional(rollbackFor = Exception.class)
public ExpenseTypeAttachmentTypeDTO createType(ExpenseTypeAttachmentTypeDTO dto) {
    dto.setTenantId(OrgInformationUtil.getCurrentTenantId());
    checkType(dto);
    // 校验唯一性 账套下的代码不能重复
    List<ExpenseType> list = baseMapper.selectList(new QueryWrapper<ExpenseType>()
            .eq("code", dto.getCode())
            .eq("set_of_books_id", dto.getSetOfBooksId())
            .eq("type_flag", dto.getTypeFlag()));
    if (!CollectionUtils.isEmpty(list)) {
        throw new BizException(dto.getTypeFlag().compareTo(0) == 0 ?
                RespCode.EXPENSE_TYPE_APPLICATION_CODE_IS_EXISTS : RespCode.EXPENSE_TYPE_EXPENSE_CODE_IS_EXISTS);
    }
    dto.setDeleted(Boolean.FALSE);
    this.insert(dto);
    checkAttchmentType(dto);
    return dto;
}

## static int **ebml_read_num**(MatroskaDemuxContext *matroska, AVIOContext *pb, int max_size, uint64_t *number, int eof_forbidden)
    读取size(去除前面的0,1后的),eof_forbidden表示是否允许到达文件结尾,返回值表示读取字节数

***

## static EbmlSyntax ***ebml_parse_id**(EbmlSyntax *syntax, uint32_t id)
    遍历syntax匹配id,成功时返回指向对应下标的指针,如果没有匹配到则返回指向最后一位的指针,即父标签的

***

## static int **ebml_read_length** MatroskaDemuxContext *matroska, AVIOContext *pb, uint64_t *number)
    通过调用ebml_read_num(matroska, pb, 8, number, 1);实现,返回值为读取的字节数

***

## static int **ebml_read_master**(MatroskaDemuxContext *matroska, uint64_t length, int64_t pos)
    如果num_levels不大于EBML_MAX_DEPTH,将pos后length的部分写入matroska->levels[num_levels]中,且num_levels增1

***

## static MatroskaLevel1Element ***matroska_find_level1_elem**(MatroskaDemuxContext *matroska, uint32_t id)
    借助matroska->num_level1_elems在matroska->level1_elems数组中找到id对应的下标的内存地址(即&matroska->level1_elems[i]),如果没找到则新增至数组中.

    不处理cluster和超出数量的部分,对于seekhead,由于数量并不唯一,因此不会在数组中查找而直接添加新项至数组中

***

## static int **ebml_parse_nest**(MatroskaDemuxContext *matroska, EbmlSyntax *syntax, void *data)
    通过syntax中数据的data_offest和def部分为data在特定位置赋值syntax[i].def,并通过赋值好的data调用ebml_parse,递归的解析syntax对应的标签
    
    返回值取决于ebml_parse是否合理的结束,若合理(即返回LEVEL_ENDED),则返回0,否则返回ebml_parse的返回值

***

## static void **ebml_free**(EbmlSyntax *syntax, void *data)
    用syntax的格式清除data中的数据,syntax应当是data实际对应数据结构的同级或上级格式,该函数运用递归实现

***

## static int **ebml_parse**(MatroskaDemuxContext *matroska, EbmlSyntax *syntax, void *data)
    IMPORTANT

    如果当前标签是NEST,那么将递归的往下解析标签,如果当前标签是具体的可保存数据的类型,则保存到data中.即调用完成后,data将表示解析后获得的数据

    此函数一次只解析一次标签(若标签含有子标签,也一并解析),即从头调用该函数时只解析了Ebml Head而没有处理Segment

***
***
***

## EbmlSyntax定义
    typedef const struct EbmlSyntax {
        uint32_t id;
        EbmlType type;
        // i don't know what it is!!!
        size_t list_elem_size;
        // 数据在相关结构体中的偏移量,用于
        // 定位data中相关数据位置
        size_t data_offset;  
        union {
            int64_t     i;
            uint64_t    u;
            double      f;
            const char *s;
            const struct EbmlSyntax *n;
        } def;  // 默认值
    } EbmlSyntax;

    EbmlSyntax定义的静态数组的最后一项用于表示当前标签的父标签,如:

    // 最顶层标签,无父标签
    static EbmlSyntax ebml_syntax[] = {
    { EBML_ID_HEADER,      EBML_NEST, 0, 0, { .n = ebml_header } },
    { MATROSKA_ID_SEGMENT, EBML_STOP },
    { 0 }
    };

    // segment中的info,父标签为segment
    static EbmlSyntax matroska_info[] = {
    { MATROSKA_ID_TIMECODESCALE, EBML_UINT,  0, offsetof(MatroskaDemuxContext, time_scale), { .u = 1000000 } },
    { MATROSKA_ID_DURATION,      EBML_FLOAT, 0, offsetof(MatroskaDemuxContext, duration) },
    { MATROSKA_ID_TITLE,         EBML_UTF8,  0, offsetof(MatroskaDemuxContext, title) },
    { MATROSKA_ID_WRITINGAPP,    EBML_NONE },
    { MATROSKA_ID_MUXINGAPP,     EBML_UTF8, 0, offsetof(MatroskaDemuxContext, muxingapp) },
    { MATROSKA_ID_DATEUTC,       EBML_BIN,  0, offsetof(MatroskaDemuxContext, date_utc) },
    { MATROSKA_ID_SEGMENTUID,    EBML_NONE },
    CHILD_OF(matroska_segment)
    };



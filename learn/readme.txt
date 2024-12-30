Git is a version control system.
Git is free software.


@Component
public abstract class ScheduleService {
    private Logger logger = LoggerFactory.getLogger(this.getClass());
    public  void responseExcute(String result, String params){
/*
v_sh600101="1~明星电力~600101~14.32~13.02~13.03~1025121~537927~487194~14.32~67936~14.31~199~14.30~226~14.29~10~14.28~29~0.00~0~0.00~0~0.00~0~0.00~0~0.00~0~~20240604155927~1.30~9.98~14.32~12.82~14.32/1025121/1388297701~1025121~138830~24.32~31.56~~14.32~12.82~11.52~60.35~60.35~2.08~14.32~11.72~0.94~68400~13.54~21.61~33.65~~~1.29~138829.7701~0.0000~0~ ~GP-A~70.27~-9.02~0.56~6.60~4.73~16.88~5.53~5.60~68.27~107.54~421432670~421432670~100.00~66.90~421432670~~~57.71~0.00~~CNY~0~___D__F__N~14.20~17";

 */
//        logger.info(""+result);
        String[] results=result.split(";");
        Stream<String> stream = Arrays.stream(results);
        String splitArg= DateTimeUtil.getyyyyMM();
        List<String> secondElements = stream.map(str ->
                {
                    String[] split = str.split(splitArg);
                    String[] split2= split[0].split("~");
                    //0:51\1    1:名字  2:code  3:当前价 4:昨天价 5:开盘 6:
                    return split2.length>4 ?      split2[1]+"~"+split2[3]+"~"+split2[4]+"~"+split2[5]:null;
                })
                .filter(element->element!=null)
                .collect(Collectors.toList());
        List<String> infos=new ArrayList<>();
        secondElements.forEach(str->{
//            System.out.println(str);
            String[] split = str.split("~");
            double x=(Double.parseDouble(split[1])-Double.parseDouble(split[2]));
            infos.add(split[0]
                    + " 涨幅："+((x/Double.parseDouble(split[2]))*100+"00000").substring(0,5)+"% "
                    +" 当前:"+split[1]
                    +" "+ (x+"00000").substring(0,5) +" "
                    +" 昨天:"+split[2]);
        });
        infos.forEach(str->{logger.info("{}",str.toString());});

//        logger.info("/n"+split[0]+" "+split[1]+" "+split[2]);
    }
}



public void execute() {
//        logger.debug("http请求分析start...");
        try {
            String url="https://qt.gtimg.cn/q=";
//            String params="";
            String params="sh600171,sh605178,sz002869,sz001298";
            if("".equals(params)){
                params= (String) stringRedisTemplate.opsForValue().get("CODESTRING");
//                logger.info("redis_params:"+params);
                if("".equals(params)||"null".equals(params)||params==null){
                    params=codeTableService.getObject().get(0).getCode();
//                    logger.info("SQL_params:"+params);
                }
            }
//            params=codeTableService.getObject().get(0).getCode();
//            QueryWrapper<CodeTable> wrapper = new QueryWrapper<>();
//            wrapper.eq("desc_type","URL_GET");
//            CodeTable codeTable=new CodeTable();
//            codeTable.setName("123");
//            codeTableService.update(codeTable,wrapper);
            String result=HttpUrlUtil.get(url,params);
            responseExcute(result,params);

        } catch (Exception e) {
            logger.error("http请求分析-数据处理执行失败:{}", e);
        }

    }

public void execute2() {
//        logger.debug("http请求分析start...");
        try {
            String url="https://stock.xueqiu.com/v5/stock/realtime/pankou.json?symbol=";

            String params="SZ159509";
//            String params="sz002916,sh600150,sh600101,sz000875,sz002466,sz002050";
            if("".equals(params)){
                params= (String) stringRedisTemplate.opsForValue().get("CODESTRING");
//                logger.info("redis_params:"+params);
                if("".equals(params)||"null".equals(params)||params==null){
                    params=codeTableService.getObject().get(0).getCode();
//                    logger.info("SQL_params:"+params);
                }
            }
//            params=codeTableService.getObject().get(0).getCode();
//            QueryWrapper<CodeTable> wrapper = new QueryWrapper<>();
//            wrapper.eq("desc_type","URL_GET");
//            CodeTable codeTable=new CodeTable();
//            codeTable.setName("123");
//            codeTableService.update(codeTable,wrapper);
            String result=HttpUrlUtil.get(url,params);
            Map<String,Object> parse = (Map) JSONObject.parse(result);
            Map<String,String> data=(Map<String, String>) parse.get("data");
            logger.info("sz159509,当前价:{}",data.get("current"));

        } catch (Exception e) {
            logger.error("http请求分析-数据处理执行失败:{}", e);
        }

    }

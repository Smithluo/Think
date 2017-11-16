# Think 笔记
摘取整句sql后，上次sql语句去掉 one() 、exists() 等查询方法
$sql->find()->createCommand()->getRawSql();去掉 上次sql语句去掉 one() 、exists() 等等
打印本次 sql


$userId = Yii::$app->user->getId(); 获取用户 id
$userModel = Yii::$app->user->identity; 获取用户模型

用户验证登录
 if (!isset(Yii::$app->user->identity)) {
    $token = Yii::$app->request->getAuthUser();
    if (!empty($token)) {
        $userModel = Users::findIdentityByAccessToken($token);
        Yii::$app->user->login($userModel);
    }
}


yii 覆盖 actions() 方法配置或禁用这些动作 
$actions = parent::actions();
unset($actions['index'], $actions['update'], $actions['create'], $actions['view'], $actions['delete']);

项目地址 D:\mp\xampp\htdocs\xiaomei360.com   购物车 index.php?m=default&c=flow&a=add_to_cart


$goodsList = Goods::find()
->select([
    'goods_id', 'goods_sn', 'goods_name', 'market_price', 'shop_price', 'discount_disable',
    'start_num', 'goods_number', 'buy_by_box', 'number_per_box', 'is_real'
])->where([
    'goods_id' => $goodsIdList,
    'is_on_sale' => Goods::IS_ON_SALE,
    'is_delete' => Goods::IS_NOT_DELETE,
])->all();
$goodsIdList = array(1,2);

<=>  SELECT `goods_id`, `goods_sn`, `goods_name`, `market_price`, `shop_price`, `discount_disable`, `start_num`, `goods_number`, `buy_by_box`, `number_per_box`, `is_real` FROM `o_goods` WHERE (`goods_id` IN (1, 2)) AND (`is_on_sale`=1) AND (`is_delete`=0)



采购心愿 http://www.xiaomei360.com/wishlist.php

取得区域名
$sql = "SELECT concat(IFNULL(c.region_name, ''), '  ', IFNULL(p.region_name, ''), " .
                "'  ', IFNULL(t.region_name, ''), '  ', IFNULL(d.region_name, '')) AS region " .
            "FROM " . $ecs->table('order_info') . " AS o " .
                "LEFT JOIN " . $ecs->table('region') . " AS c ON o.country = c.region_id " .
                "LEFT JOIN " . $ecs->table('region') . " AS p ON o.province = p.region_id " .
                "LEFT JOIN " . $ecs->table('region') . " AS t ON o.city = t.region_id " .
                "LEFT JOIN " . $ecs->table('region') . " AS d ON o.district = d.region_id " .
            "WHERE o.order_id = '$order[order_id]'";
    $order['region'] = $db->getOne($sql);


	/**
	 * 
     * 选择一个随机的方案 生成订单的唯一编码
     * 判重，如果orderSn 重复，递归
     * @return string
     */
    public static function getUniqueOrderSn($date = null)
    {
        mt_srand((double) microtime() * 1000000);

        if (empty($date)) {
            $date = date('Ymd');
        }

        $orderSn = $date . str_pad(mt_rand(1, 99999), 5, '0', STR_PAD_LEFT);

        //  判重
        $record = OrderInfo::find()->where(['order_sn' => $orderSn])->one();
        if (!empty($record)) {
            return self::getUniqueOrderSn($date);
        } else {
            return $orderSn;
        }
    }


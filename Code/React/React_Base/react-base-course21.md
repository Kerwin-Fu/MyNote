---
title: 21_极客园 H5
date: 2022-01-08 12:48:22
tags:
---

## 今日目标

✔ 评论。

<!-- more -->

## 文章评论-获取评论数据

-   data.d.ts

```ts
export type Comment = {
    aut_id: string
    aut_name: string
    aut_photo: string
    com_id: string
    content: string
    is_followed: boolean
    is_liking: boolean
    like_count: number
    pubdate: string
    reply_count: number
}

export type CommentRes = {
    end_id: string
    last_id: string
    results: Comment[]
    total_count: number
}
```

-   `actions/articles.ts`

```ts
export function getCommentList(id: string, offset?: string): RootThunkAction {
    return async (dispatch) => {
        const res = await request.get<ApiResponse<CommentRes>>('/comments', {
            params: {
                type: 'a',
                source: id,
                offset,
            },
        })
        console.log(res)
    }
}
```

-   组件中发送请求，`pages/Article/index.tsx`

```ts
useInitState(() => getCommentList(articleId), 'article')
```

## 文章评论-保存到 Redux 中

-   `types/store.d.ts`

```ts
export type ArticleAction =
    | {
          type: 'article/setArticleInfo'
          payload: ArticleDetail
      }
    | {
          type: 'article/saveComment'
          payload: CommentRes
      }
```

-   actions/article.ts

```ts
export function getCommentList(id: string, offset?: string): RootThunkAction {
    return async (dispatch) => {
        const res = await request.get<ApiResponse<CommentRes>>('/comments', {
            params: {
                type: 'a',
                source: id,
                offset,
            },
        })
        dispatch({
            type: 'article/saveComment',
            payload: res.data.data,
        })
    }
}
```

-   reducers/article.ts

```ts
import { ArticleDetail, CommentRes } from '@/types/data'
import { ArticleAction } from '@/types/store'
type ArticleType = {
    info: ArticleDetail
    comment: CommentRes
}
const initState: ArticleType = {
    info: {},
    comment: {},
} as ArticleType

export default function article(state = initState, action: ArticleAction): ArticleType {
    switch (action.type) {
        case 'article/setArticleInfo':
            return {
                ...state,
                info: action.payload,
            }
        case 'article/saveComment':
            const old = state.comment.results || []
            return {
                ...state,
                comment: {
                    ...action.payload,
                    results: [...old, ...action.payload.results],
                },
            }
        default:
            return state
    }
}
```

## 文章评论-基本渲染

-   准备 noCommnet 组件，素材中已经准备好

-   准备 CommentItem 组件，素材中已经准备好

-   获取评论数据并渲染

```ts
const { comment } = useInitState(() => getCommentList(articleId), 'article')

<div className='comment'>
    <div className='comment-header'>
        <span>全部评论（{comment.total_count}）</span>
        <span>{info.like_count} 点赞</span>
    </div>
    <div className='comment-list'>
        {info.comm_count === 0 ? <NoComment /> : comment.results?.map((item) => <CommentItem key={item.com_id} />)}

        <InfiniteScroll
            hasMore={false}
            loadMore={async () => {
                console.log(1)
            }}
        />
    </div>
</div>
```

## 文章评论-渲染评论列表

`pages/Article/index.tsx`

```ts
<div className='comment-list'>
    {info.comm_count === 0 ? (
        <NoComment />
    ) : (
        comment.results?.map((item) => (
            // #1
            <CommentItem key={item.com_id} comment={item} type='normal' />
        ))
    )}

    <InfiniteScroll
        hasMore={false}
        loadMore={async () => {
            console.log(1)
        }}
    />
</div>
```

`pages/Article/components/CommentItem/index.tsx`

```ts
import dayjs from 'dayjs'
import classnames from 'classnames'
import Icon from '@/components/Icon'
import styles from './index.module.scss'
import { Comment } from '@/types/data'

type Props = {
    // normal 普通 - 文章的评论
    // origin 回复评论的原始评论，也就是对哪个评论进行回复
    // reply 回复评论
    type?: 'normal' | 'reply' | 'origin'
    // #2
    comment: Comment
}

const CommentItem = ({
    // normal 普通
    // origin 回复评论的原始评论
    // reply 回复评论
    type = 'normal',
    // #3
    comment,
}: Props) => {
    // 回复按钮
    const replyJSX =
        type === 'normal' ? (
            <span className='replay'>
                {comment.reply_count} 回复
                <Icon type='iconbtn_right' />
            </span>
        ) : null

    return (
        <div className={styles.root}>
            <div className='avatar'>
                <img src={comment.aut_photo} alt='' />
            </div>
            <div className='comment-info'>
                <div className='comment-info-header'>
                    <span className='name'>{comment.aut_name}</span>
                    {/* 文章评论、评论的回复 */}
                    {(type === 'normal' || type === 'reply') && (
                        <span className='thumbs-up'>
                            {comment.like_count}
                            <Icon type={comment.is_liking ? 'iconbtn_like_sel' : 'iconbtn_like2'} />
                        </span>
                    )}
                    {/* 要回复的评论 */}
                    {type === 'origin' && <span className={classnames('follow', comment.is_followed ? 'followed' : '')}>{comment.is_followed ? '已关注' : '关注'}</span>}
                </div>
                <div className='comment-content'>{comment.content}</div>
                <div className='comment-footer'>
                    {replyJSX}
                    {/* 非评论的回复 */}
                    {type !== 'reply' && <span className='comment-time'>{dayjs(comment.pubdate).fromNow()}</span>}
                    {/* 文章的评论 */}
                    {type === 'origin' && (
                        <span className='thumbs-up'>
                            {comment.like_count}
                            <Icon type={comment.is_liking ? 'iconbtn_like_sel' : 'iconbtn_like2'} />
                        </span>
                    )}
                </div>
            </div>
        </div>
    )
}

export default CommentItem
```

## 组件销毁时清除评论数据

Bug 重现：多次进入同一篇带评论的文章试试。

`store.d.ts`

```ts
export type ArticleAction =
    | {
          type: 'article/setArticleInfo'
          payload: ArticleDetail
      }
    | {
          type: 'article/saveComment'
          payload: CommentRes
      }
    | {
          type: 'article/clearComment'
      }
```

`actions/article.ts`

```ts
export function clearCommentList(): ArticleAction {
    return {
        type: 'article/clearComment',
    }
}
```

`reducers/article.ts`

```ts
import { ArticleDetail, CommentRes } from '@/types/data'
import { ArticleAction } from '@/types/store'
type ArticleType = {
    info: ArticleDetail
    comment: CommentRes
}
const initState: ArticleType = {
    info: {},
    comment: {},
} as ArticleType

export default function article(state = initState, action: ArticleAction): ArticleType {
    switch (action.type) {
        case 'article/setArticleInfo':
            return {
                ...state,
                info: action.payload,
            }
        case 'article/saveComment':
            const old = state.comment.results || []
            return {
                ...state,
                comment: {
                    ...action.payload,
                    results: [...old, ...action.payload.results],
                },
            }
        case 'article/clearComment':
            return {
                ...state,
                comment: {} as CommentRes,
            }
        default:
            return state
    }
}
```

`pages/Article/index.tsx`

```ts
useEffect(() => {
    return () => {
        dispatch(clearCommentList())
    }
}, [dispatch])
```

## 文章评论-触底加载更多

```ts
import { useEffect, useState, useRef } from 'react'
import { NavBar, InfiniteScroll } from 'antd-mobile'
import { useDispatch, useSelector } from 'react-redux'
import { useHistory, useParams } from 'react-router-dom'
import classNames from 'classnames'
import styles from './index.module.scss'
import Icon from '@/components/Icon'
import CommentItem from './components/CommentItem'
import CommentFooter from './components/CommentFooter'
import { clearCommentList, getArticleInfo, getCommentList } from '@/store/actions/article'
import dayjs from 'dayjs'
import { useInitState } from '@/utils/hooks'
import dompurify from 'dompurify'
import hljs from 'highlight.js'
import 'highlight.js/styles/vs2015.css'
import NoComment from './components/NoComment'
import { RootState } from '@/types/store'

const Article = () => {
    const history = useHistory()
    const dispatch = useDispatch()
    const params = useParams<{ id: string }>()
    const articleId = params.id
    const [isShowAuthor, setIsShowAuthor] = useState(false)
    const authorRef = useRef<HTMLDivElement>(null)
    const wrapRef = useRef<HTMLDivElement>(null)
    const { info } = useInitState(() => getArticleInfo(articleId), 'article')
    // #1
    const {
        results = [],
        total_count = -1,
        last_id,
        // end_id = '',
    } = useSelector((state: RootState) => state.article.comment)
    // #2 不相等，表示还有更多
    // const hasMore = last_id !== end_id
    const hasMore = results.length !== total_count
    // #3
    const loadMore = async () => {
        console.log('加载更多~~')
        await dispatch(getCommentList(params.id, last_id))
    }
    useEffect(() => {
        // 配置 highlight.js
        hljs.configure({
            // 忽略未经转义的 HTML 字符
            ignoreUnescapedHTML: true,
        })
        // 获取到内容中所有的code标签
        const codes = document.querySelectorAll('.dg-html pre code')
        codes.forEach((el) => {
            // 让code进行高亮
            hljs.highlightElement(el as HTMLElement)
        })
    }, [articleId])
    useEffect(() => {
        const wrapDOM = wrapRef.current!
        const authDOM = authorRef.current!
        const onScroll = function () {
            const rect = authDOM.getBoundingClientRect()!
            if (rect.top <= 0) {
                setIsShowAuthor(true)
            } else {
                setIsShowAuthor(false)
            }
        }
        wrapDOM.addEventListener('scroll', onScroll)
        return () => {
            wrapDOM.removeEventListener('scroll', onScroll)
        }
    }, [])
    useEffect(() => {
        return () => {
            dispatch(clearCommentList())
        }
    }, [dispatch])
    return (
        <div className={styles.root}>
            <div className='root-wrapper'>
                <NavBar
                    onBack={() => history.go(-1)}
                    right={
                        <span>
                            <Icon type='icongengduo' />
                        </span>
                    }
                >
                    {isShowAuthor && (
                        <div className='nav-author'>
                            <img src={info.aut_photo} alt='' />
                            <span className='name'>{info.aut_name}</span>
                            <span className={classNames('follow', info.is_followed ? 'followed' : '')}>{info.is_followed ? '已关注' : '关注'}</span>
                        </div>
                    )}
                </NavBar>
                {/* //!文章详情和评论 */}
                <div className='wrapper' ref={wrapRef}>
                    <div className='article-wrapper'>
                        <div className='header'>
                            <h1 className='title'>{info.title}</h1>
                            <div className='info'>
                                <span>{dayjs(info.pubdate).format('YYYY-MM-DD')}</span>
                                <span>{info.read_count} 阅读</span>
                                <span>{info.comm_count} 评论</span>
                            </div>
                            <div className='author' ref={authorRef}>
                                <img src={info.aut_photo} alt='' />
                                <span className='name'>{info.aut_name}</span>
                                <span className={classNames('follow', info.is_followed ? 'followed' : '')}>{info.is_followed ? '已关注' : '关注'}</span>
                            </div>
                        </div>

                        <div className='content'>
                            <div
                                className='content-html dg-html'
                                dangerouslySetInnerHTML={{
                                    __html: dompurify.sanitize(info.content || ''),
                                }}
                            />
                            <div className='date'>发布文章时间：{dayjs(info.pubdate).format('YYYY-MM-DD')}</div>
                        </div>
                    </div>
                    {/* //!评论信息 */}
                    <div className='comment'>
                        <div className='comment-header'>
                            <span>全部评论（{total_count}）</span>
                            <span>{info.like_count} 点赞</span>
                        </div>
                        <div className='comment-list'>
                            {info.comm_count === 0 ? <NoComment /> : results?.map((item) => <CommentItem key={item.com_id} comment={item} type='normal' />)}

                            <InfiniteScroll hasMore={hasMore} loadMore={loadMore} />
                        </div>
                    </div>
                </div>
                {/* //!底部评论栏 */}
                <CommentFooter />
            </div>
        </div>
    )
}

export default Article
```

## 文章评论-底部的渲染

`pages/Article/components/CommentFooter/index.tsx`

```ts
import { useSelector } from 'react-redux'
import Icon from '@/components/Icon'
import styles from './index.module.scss'
import { RootState } from '@/types/store'

type Props = {
    // normal 普通评论
    // reply 回复评论
    type?: 'normal' | 'reply'
}

const CommentFooter = ({ type = 'normal' }: Props) => {
    const { info } = useSelector((state: RootState) => state.article)
    return (
        <div className={styles.root}>
            <div className='input-btn'>
                <Icon type='iconbianji' />
                <span>抢沙发</span>
            </div>

            {type === 'normal' && (
                <>
                    <div className='action-item'>
                        <Icon type='iconbtn_comment' />
                        <p>评论</p>
                        {info.comm_count && <span className='bage'>{info.comm_count}</span>}
                    </div>
                    <div className='action-item'>
                        <Icon type={info.attitude === 1 ? 'iconbtn_like_sel' : 'iconbtn_like2'} />
                        <p>点赞</p>
                    </div>
                    <div className='action-item'>
                        <Icon type={info.is_collected ? 'iconbtn_collect_sel' : 'iconbtn_collect'} />
                        <p>收藏</p>
                    </div>
                </>
            )}

            {type === 'reply' && (
                <div className='action-item'>
                    <Icon type={info.attitude === 1 ? 'iconbtn_like_sel' : 'iconbtn_like2'} />
                    <p>点赞</p>
                </div>
            )}

            <div className='action-item'>
                <Icon type='iconbtn_share' />
                <p>分享</p>
            </div>
        </div>
    )
}

export default CommentFooter
```

## 文章点赞功能

`pages/Article/components/CommentFooter/index.tsx`

```ts
const { info } = useSelector((state: RootState) => state.article)
const onLike = async () => {
    await dispatch(likeAritcle(info.art_id, info.attitude))
}
```

`actions/article.ts`

```ts
export function likeAritcle(id: string, attitude: number): RootThunkAction {
    return async (dispatch) => {
        if (attitude === 1) {
            // 取消点赞
            await request.delete('/article/likings/' + id)
        } else {
            // 点赞
            await request.post('/article/likings', { target: id })
        }
        // 更新
        await dispatch(getArticleInfo(id))
    }
}
```

## 文章收藏功能

`actions/article.ts`

```ts
export function collectArticle(id: string, is_collected: boolean): RootThunkAction {
    return async (dispatch) => {
        if (is_collected) {
            // 取消收藏
            await request.delete('/article/collections/' + id)
        } else {
            // 收藏
            await request.post('/article/collections', {
                target: id,
            })
        }
        await dispatch(getArticleInfo(id))
    }
}
```

`pages\Article\components\CommentFooter\index.tsx`

```ts
const collect = async () => {
    dispatch(collectArticle(info.art_id, info.is_collected))
}
```

## 关注用户功能

`actions/article.ts`

```ts
export function followUser(userId: string, is_follow: boolean): RootThunkAction {
    return async (dispatch, getState) => {
        if (is_follow) {
            // 取消关注
            await request.delete('/user/followings/' + userId)
        } else {
            // 关注
            await request.post('/user/followings', {
                target: userId,
            })
        }
        await dispatch(getArticleInfo(getState().article.info.art_id))
    }
}
```

`pages\Article\index.tsx`

```ts
const onFollowUser = async () => {
    await dispatch(followUser(info.aut_id, info.is_followed))
    Toast.show('操作成功')
}
```

## 文章评论-点击评论位置跳转

-   `pages/Article/index.tsx` 父组件提供方法。

```ts
import { useEffect, useState, useRef } from 'react'
import { NavBar, InfiniteScroll, Toast } from 'antd-mobile'
import { useDispatch, useSelector } from 'react-redux'
import { useHistory, useParams } from 'react-router-dom'
import classNames from 'classnames'
import styles from './index.module.scss'
import Icon from '@/components/Icon'
import CommentItem from './components/CommentItem'
import CommentFooter from './components/CommentFooter'
import { clearCommentList, followUser, getArticleInfo, getCommentList } from '@/store/actions/article'
import dayjs from 'dayjs'
import { useInitState } from '@/utils/hooks'
import dompurify from 'dompurify'
import hljs from 'highlight.js'
import 'highlight.js/styles/vs2015.css'
import NoComment from './components/NoComment'
import { RootState } from '@/types/store'

const Article = () => {
    const history = useHistory()
    const dispatch = useDispatch()
    const params = useParams<{ id: string }>()
    const articleId = params.id
    const [isShowAuthor, setIsShowAuthor] = useState(false)
    const authorRef = useRef<HTMLDivElement>(null)
    const wrapRef = useRef<HTMLDivElement>(null)
    // #1
    const commentRef = useRef<HTMLDivElement>(null)
    // #2
    const [isComment, setIsComment] = useState(false)
    // #3
    const onComment = () => {
        const commentDOM = commentRef.current!
        const wrapDOM = wrapRef.current!
        if (isComment) {
            wrapDOM.scrollTo(0, 0)
        } else {
            wrapDOM.scrollTo(0, commentDOM.offsetTop - 44)
        }
        setIsComment(!isComment)
    }
    const { info } = useInitState(() => getArticleInfo(articleId), 'article')
    const {
        results = [],
        total_count = -1,
        last_id,
        // end_id = '',
    } = useSelector((state: RootState) => state.article.comment)
    // const hasMore = last_id !== end_id
    const hasMore = results.length !== total_count
    const loadMore = async () => {
        console.log('加载更多~~')
        await dispatch(getCommentList(params.id, last_id))
    }
    const onFollowUser = async () => {
        await dispatch(followUser(info.aut_id, info.is_followed))
        Toast.show('操作成功')
    }
    useEffect(() => {
        // 配置 highlight.js
        hljs.configure({
            // 忽略未经转义的 HTML 字符
            ignoreUnescapedHTML: true,
        })
        // 获取到内容中所有的code标签
        const codes = document.querySelectorAll('.dg-html pre code')
        codes.forEach((el) => {
            // 让code进行高亮
            hljs.highlightElement(el as HTMLElement)
        })
    }, [articleId])
    useEffect(() => {
        const wrapDOM = wrapRef.current!
        const authDOM = authorRef.current!
        const onScroll = function () {
            const rect = authDOM.getBoundingClientRect()!
            if (rect.top <= 0) {
                setIsShowAuthor(true)
            } else {
                setIsShowAuthor(false)
            }
        }
        wrapDOM.addEventListener('scroll', onScroll)
        return () => {
            wrapDOM.removeEventListener('scroll', onScroll)
        }
    }, [])
    useEffect(() => {
        return () => {
            dispatch(clearCommentList())
        }
    }, [dispatch])
    return (
        <div className={styles.root}>
            <div className='root-wrapper'>
                <NavBar
                    onBack={() => history.go(-1)}
                    right={
                        <span>
                            <Icon type='icongengduo' />
                        </span>
                    }
                >
                    {isShowAuthor && (
                        <div className='nav-author'>
                            <img src={info.aut_photo} alt='' />
                            <span className='name'>{info.aut_name}</span>
                            <span className={classNames('follow', info.is_followed ? 'followed' : '')} onClick={onFollowUser}>
                                {info.is_followed ? '已关注' : '关注'}
                            </span>
                        </div>
                    )}
                </NavBar>
                {/* //!文章详情和评论 */}
                <div className='wrapper' ref={wrapRef}>
                    <div className='article-wrapper'>
                        <div className='header'>
                            <h1 className='title'>{info.title}</h1>
                            <div className='info'>
                                <span>{dayjs(info.pubdate).format('YYYY-MM-DD')}</span>
                                <span>{info.read_count} 阅读</span>
                                <span>{info.comm_count} 评论</span>
                            </div>
                            <div className='author' ref={authorRef}>
                                <img src={info.aut_photo} alt='' />
                                <span className='name'>{info.aut_name}</span>
                                <span className={classNames('follow', info.is_followed ? 'followed' : '')} onClick={onFollowUser}>
                                    {info.is_followed ? '已关注' : '关注'}
                                </span>
                            </div>
                        </div>

                        <div className='content'>
                            <div
                                className='content-html dg-html'
                                dangerouslySetInnerHTML={{
                                    __html: dompurify.sanitize(info.content || ''),
                                }}
                            />
                            <div className='date'>发布文章时间：{dayjs(info.pubdate).format('YYYY-MM-DD')}</div>
                        </div>
                    </div>
                    {/* //!评论信息 */}
                    <div className='comment'>
                        <div className='comment-header' ref={commentRef}>
                            <span>全部评论（{total_count}）</span>
                            <span>{info.like_count} 点赞</span>
                        </div>
                        <div className='comment-list'>
                            {info.comm_count === 0 ? <NoComment /> : results?.map((item) => <CommentItem key={item.com_id} comment={item} type='normal' />)}

                            <InfiniteScroll hasMore={hasMore} loadMore={loadMore} />
                        </div>
                    </div>
                </div>
                {/* //!底部评论栏 */}
                <CommentFooter onComment={onComment} />
            </div>
        </div>
    )
}

export default Article
```

-   `pages/Article/components/CommentFooter/index.tsx` 子组件触发。

```ts
import { useSelector, useDispatch } from 'react-redux'
import Icon from '@/components/Icon'
import styles from './index.module.scss'
import { RootState } from '@/types/store'
import { collectArticle, likeAritcle } from '@/store/actions/article'

type Props = {
    // normal 普通评论
    // reply 回复评论
    type?: 'normal' | 'reply'
    onComment?: () => void
}

const CommentFooter = ({ type = 'normal', onComment }: Props) => {
    const dispatch = useDispatch()
    const { info } = useSelector((state: RootState) => state.article)
    const onLike = async () => {
        await dispatch(likeAritcle(info.art_id, info.attitude))
    }
    const onCollect = async () => {
        dispatch(collectArticle(info.art_id, info.is_collected))
    }
    return (
        <div className={styles.root}>
            <div className='input-btn'>
                <Icon type='iconbianji' />
                <span>抢沙发</span>
            </div>

            {type === 'normal' && (
                <>
                    <div className='action-item' onClick={onComment}>
                        <Icon type='iconbtn_comment' />
                        <p>评论</p>
                        {info.comm_count && <span className='bage'>{info.comm_count}</span>}
                    </div>
                    <div className='action-item' onClick={onLike}>
                        <Icon type={info.attitude === 1 ? 'iconbtn_like_sel' : 'iconbtn_like2'} />
                        <p>点赞</p>
                    </div>
                    <div className='action-item' onClick={onCollect}>
                        <Icon type={info.is_collected ? 'iconbtn_collect_sel' : 'iconbtn_collect'} />
                        <p>收藏</p>
                    </div>
                </>
            )}

            {type === 'reply' && (
                <div className='action-item'>
                    <Icon type={info.attitude === 1 ? 'iconbtn_like_sel' : 'iconbtn_like2'} />
                    <p>点赞</p>
                </div>
            )}

            <div className='action-item'>
                <Icon type='iconbtn_share' />
                <p>分享</p>
            </div>
        </div>
    )
}

export default CommentFooter
```

## 文章评论-封装评论表单组件

素材中已经提供好了，把 CommentInput 粘贴到 pages/Article/components 文件夹。

## 文章评论-显示评论表单组件

-   pages/Article/index.tsx

```ts
import { useEffect, useState, useRef } from 'react'
import { NavBar, InfiniteScroll, Toast, Popup } from 'antd-mobile'
import { useDispatch, useSelector } from 'react-redux'
import { useHistory, useParams } from 'react-router-dom'
import classNames from 'classnames'
import styles from './index.module.scss'
import Icon from '@/components/Icon'
import CommentItem from './components/CommentItem'
import CommentFooter from './components/CommentFooter'
import { clearCommentList, followUser, getArticleInfo, getCommentList } from '@/store/actions/article'
import dayjs from 'dayjs'
import { useInitState } from '@/utils/hooks'
import dompurify from 'dompurify'
import hljs from 'highlight.js'
import 'highlight.js/styles/vs2015.css'
import NoComment from './components/NoComment'
import { RootState } from '@/types/store'
import CommentInput from './components/CommentInput'

const Article = () => {
    const history = useHistory()
    const dispatch = useDispatch()
    const params = useParams<{ id: string }>()
    const articleId = params.id
    const [isShowAuthor, setIsShowAuthor] = useState(false)
    const authorRef = useRef<HTMLDivElement>(null)
    const wrapRef = useRef<HTMLDivElement>(null)
    const commentRef = useRef<HTMLDivElement>(null)
    const [isComment, setIsComment] = useState(false)
    const onComment = () => {
        const commentDOM = commentRef.current!
        const wrapDOM = wrapRef.current!
        if (isComment) {
            wrapDOM.scrollTo(0, 0)
        } else {
            wrapDOM.scrollTo(0, commentDOM.offsetTop - 44)
        }
        setIsComment(!isComment)
    }
    const { info } = useInitState(() => getArticleInfo(articleId), 'article')
    const {
        results = [],
        total_count = -1,
        last_id,
        // end_id = '',
    } = useSelector((state: RootState) => state.article.comment)
    // const hasMore = last_id !== end_id
    const hasMore = results.length !== total_count
    const loadMore = async () => {
        console.log('加载更多~~')
        await dispatch(getCommentList(params.id, last_id))
    }
    const onFollowUser = async () => {
        await dispatch(followUser(info.aut_id, info.is_followed))
        Toast.show('操作成功')
    }
    // #3
    const [commentShow, setCommentShow] = useState(false)
    const hideComment = () => {
        setCommentShow(false)
    }
    const showComment = () => {
        setCommentShow(true)
    }
    useEffect(() => {
        // 配置 highlight.js
        hljs.configure({
            // 忽略未经转义的 HTML 字符
            ignoreUnescapedHTML: true,
        })
        // 获取到内容中所有的code标签
        const codes = document.querySelectorAll('.dg-html pre code')
        codes.forEach((el) => {
            // 让code进行高亮
            hljs.highlightElement(el as HTMLElement)
        })
    }, [articleId])
    useEffect(() => {
        const wrapDOM = wrapRef.current!
        const authDOM = authorRef.current!
        const onScroll = function () {
            const rect = authDOM.getBoundingClientRect()!
            if (rect.top <= 0) {
                setIsShowAuthor(true)
            } else {
                setIsShowAuthor(false)
            }
        }
        wrapDOM.addEventListener('scroll', onScroll)
        return () => {
            wrapDOM.removeEventListener('scroll', onScroll)
        }
    }, [])
    useEffect(() => {
        return () => {
            dispatch(clearCommentList())
        }
    }, [dispatch])
    return (
        <div className={styles.root}>
            <div className='root-wrapper'>
                <NavBar
                    onBack={() => history.go(-1)}
                    right={
                        <span>
                            <Icon type='icongengduo' />
                        </span>
                    }
                >
                    {isShowAuthor && (
                        <div className='nav-author'>
                            <img src={info.aut_photo} alt='' />
                            <span className='name'>{info.aut_name}</span>
                            <span className={classNames('follow', info.is_followed ? 'followed' : '')} onClick={onFollowUser}>
                                {info.is_followed ? '已关注' : '关注'}
                            </span>
                        </div>
                    )}
                </NavBar>
                {/* //!文章详情和评论 */}
                <div className='wrapper' ref={wrapRef}>
                    <div className='article-wrapper'>
                        <div className='header'>
                            <h1 className='title'>{info.title}</h1>
                            <div className='info'>
                                <span>{dayjs(info.pubdate).format('YYYY-MM-DD')}</span>
                                <span>{info.read_count} 阅读</span>
                                <span>{info.comm_count} 评论</span>
                            </div>
                            <div className='author' ref={authorRef}>
                                <img src={info.aut_photo} alt='' />
                                <span className='name'>{info.aut_name}</span>
                                <span className={classNames('follow', info.is_followed ? 'followed' : '')} onClick={onFollowUser}>
                                    {info.is_followed ? '已关注' : '关注'}
                                </span>
                            </div>
                        </div>

                        <div className='content'>
                            <div
                                className='content-html dg-html'
                                dangerouslySetInnerHTML={{
                                    __html: dompurify.sanitize(info.content || ''),
                                }}
                            />
                            <div className='date'>发布文章时间：{dayjs(info.pubdate).format('YYYY-MM-DD')}</div>
                        </div>
                    </div>
                    {/* //!评论信息 */}
                    <div className='comment'>
                        <div className='comment-header' ref={commentRef}>
                            <span>全部评论（{total_count}）</span>
                            <span>{info.like_count} 点赞</span>
                        </div>
                        <div className='comment-list'>
                            {info.comm_count === 0 ? <NoComment /> : results?.map((item) => <CommentItem key={item.com_id} comment={item} type='normal' />)}
                            <InfiniteScroll hasMore={hasMore} loadMore={loadMore} />
                        </div>
                    </div>
                </div>
                {/* //!底部评论栏 */}
                {/* #2 */}
                <CommentFooter onComment={onComment} showComment={showComment} />
            </div>
            {/* #1 */}
            <Popup visible={commentShow} position='right' destroyOnClose>
                <CommentInput hideComment={hideComment} />
            </Popup>
        </div>
    )
}

export default Article
```

`pages/Article/components/CommentFooter/index.tsx`

```ts
import { useSelector, useDispatch } from 'react-redux'
import Icon from '@/components/Icon'
import styles from './index.module.scss'
import { RootState } from '@/types/store'
import { collectArticle, likeAritcle } from '@/store/actions/article'

type Props = {
    // normal 普通评论
    // reply 回复评论
    type?: 'normal' | 'reply'
    onComment?: () => void
    showComment: () => void
}

const CommentFooter = ({ type = 'normal', onComment, showComment }: Props) => {
    const dispatch = useDispatch()
    const { info } = useSelector((state: RootState) => state.article)
    const onLike = async () => {
        await dispatch(likeAritcle(info.art_id, info.attitude))
    }
    const onCollect = async () => {
        dispatch(collectArticle(info.art_id, info.is_collected))
    }
    return (
        <div className={styles.root}>
            {/* #4 */}
            <div className='input-btn' onClick={showComment}>
                <Icon type='iconbianji' />
                <span>抢沙发</span>
            </div>

            {type === 'normal' && (
                <>
                    <div className='action-item' onClick={onComment}>
                        <Icon type='iconbtn_comment' />
                        <p>评论</p>
                        {info.comm_count && <span className='bage'>{info.comm_count}</span>}
                    </div>
                    <div className='action-item' onClick={onLike}>
                        <Icon type={info.attitude === 1 ? 'iconbtn_like_sel' : 'iconbtn_like2'} />
                        <p>点赞</p>
                    </div>
                    <div className='action-item' onClick={onCollect}>
                        <Icon type={info.is_collected ? 'iconbtn_collect_sel' : 'iconbtn_collect'} />
                        <p>收藏</p>
                    </div>
                </>
            )}

            {type === 'reply' && (
                <div className='action-item'>
                    <Icon type={info.attitude === 1 ? 'iconbtn_like_sel' : 'iconbtn_like2'} />
                    <p>点赞</p>
                </div>
            )}

            <div className='action-item'>
                <Icon type='iconbtn_share' />
                <p>分享</p>
            </div>
        </div>
    )
}

export default CommentFooter
```

`pages/Article/components/CommentInput/index.tsx`

```ts
import { useRef, useEffect } from 'react'
import styles from './index.module.scss'
import { NavBar, TextArea } from 'antd-mobile'
import { TextAreaRef } from 'antd-mobile/es/components/text-area'
type Props = {
    // 评论的作者的名字
    name?: string
    hideComment: () => void
}
export default function CommentInput({ name, hideComment }: Props) {
    const textRef = useRef<TextAreaRef>(null)
    // 自动获取焦点
    useEffect(() => textRef.current?.focus(), [])
    return (
        <div className={styles.root}>
            {/* #5 */}
            <NavBar right={<span className='publish'>发表</span>} onBack={hideComment}>
                {name ? '回复评论' : '评论文章'}
            </NavBar>
            <div className='input-area'>
                {/* 回复别人的评论时显示：@某某 */}
                {name && <div className='at'>@{name}:</div>}
                {/* 评论内容输入框 */}
                <TextArea placeholder='说点什么~' rows={10} ref={textRef} />
            </div>
        </div>
    )
}
```

## 文章评论-发表评论功能

1. 点击按钮把输入的内容传递到父组件。

`pages/Article/components/CommentInput/index.tsx`

```ts
// #1
const [comment, setComment] = useState('')
// #2
const onPublishComment = () => {
    if (!comment) return
    onAddComment && onAddComment(comment)
    hideComment()
}
```

2. 父组件根据收到的内容发送请求。

`store/actions/article.ts`

```ts
export function addComment(articleId: string, content: string): RootThunkAction {
    return async (dispatch, getState) => {
        const res = await request.post<ApiResponse<{ new_obj: Comment }>>('/comments', {
            target: articleId,
            content,
        })
        console.log(res.data.data.new_obj)
    }
}
```

`pages/Article/index.tsx`

```ts
const onAddComment = (comment: string) => {
    // #3
    dispatch(addComment(articleId, comment))
}
```

3. 保存评论数据到 Redux 中。

`tore.d.ts`

```ts
export type ArticleAction =
    | {
          type: 'article/setArticleInfo'
          payload: ArticleDetail
      }
    | {
          type: 'article/saveComment'
          payload: CommentRes
      }
    | {
          type: 'article/clearComment'
      }
    | {
          type: 'article/saveNewComment'
          payload: Comment
      }
```

`store/actions/article.ts`

```ts
export function addComment(articleId: string, content: string): RootThunkAction {
    return async (dispatch) => {
        const res = await request.post<ApiResponse<{ new_obj: Comment }>>('/comments', {
            target: articleId,
            content,
        })
        dispatch({
            type: 'article/saveNewComment',
            payload: res.data.data.new_obj,
        })
    }
}
```

`store/reducers/article.ts`

```ts
import { ArticleDetail, CommentRes } from '@/types/data'
import { ArticleAction } from '@/types/store'
type ArticleType = {
    info: ArticleDetail
    comment: CommentRes
}
const initState: ArticleType = {
    info: {},
    comment: {},
} as ArticleType

export default function article(state = initState, action: ArticleAction): ArticleType {
    switch (action.type) {
        case 'article/setArticleInfo':
            return {
                ...state,
                info: action.payload,
            }
        case 'article/saveComment':
            const old = state.comment.results || []
            return {
                ...state,
                comment: {
                    ...action.payload,
                    results: [...old, ...action.payload.results],
                },
            }
        case 'article/clearComment':
            return {
                ...state,
                comment: {} as CommentRes,
            }
        case 'article/saveNewComment':
            return {
                ...state,
                comment: {
                    ...state.comment,
                    results: [action.payload, ...state.comment.results],
                },
            }
        default:
            return state
    }
}
```

4. 更新评论数量。

```ts
const onAddComment = async (comment: string) => {
    // #3
    await dispatch(addComment(articleId, comment))
    await dispatch(getArticleInfo(articleId))
    Toast.show({ icon: 'success', content: '发表成功' })
}
```

5. `pages/Article/index.jsx`

```ts
// 之前用到是 total_count，改成 info.comm_count
<span>全部评论（{info.comm_count}）</span>
```

## 评论回复-弹框控制

1. 把素材中的 CommentReply 组件粘贴到 `src\pages\Article\components` 文件夹。

2. `src\pages\Article\components\CommentItem\index.tsx`，点击回复按钮展示弹框。

```ts
// 父组件：src\pages\Article\index.tsx
const [replyShow, setReplyShow] = useState({
    visible: false,
    origin: {} as Comment,
})
const hideReply = () => {
    setReplyShow({
        visible: false,
        origin: {} as Comment,
    })
}
const onReply = (comment: Comment) => {
    setReplyShow({
        visible: true,
        origin: comment,
    })
}
```

3. `src\pages\Article\components\CommentReply\index.tsx`

点击返回按钮，关闭弹框。

```ts
type Props = {
    onHideReply: () => void
}
```

```ts
// src\pages\Article\index.tsx
<CommentReply onHideReply={hideReply} />
```

4. 完整代码

`src\pages\Article\index.tsx`

```ts
import { useEffect, useState, useRef } from 'react'
import { NavBar, InfiniteScroll, Toast, Popup } from 'antd-mobile'
import { useDispatch, useSelector } from 'react-redux'
import { useHistory, useParams } from 'react-router-dom'
import classNames from 'classnames'
import styles from './index.module.scss'
import Icon from '@/components/Icon'
import CommentItem from './components/CommentItem'
import CommentFooter from './components/CommentFooter'
import { addComment, clearCommentList, followUser, getArticleInfo, getCommentList } from '@/store/actions/article'
import dayjs from 'dayjs'
import { useInitState } from '@/utils/hooks'
import dompurify from 'dompurify'
import hljs from 'highlight.js'
import 'highlight.js/styles/vs2015.css'
import NoComment from './components/NoComment'
import { RootState } from '@/types/store'
import CommentInput from './components/CommentInput'
import CommentReply from './components/CommentReply'
import { Comment } from '@/types/data'

const Article = () => {
    const history = useHistory()
    const dispatch = useDispatch()
    const params = useParams<{ id: string }>()
    const articleId = params.id
    const [isShowAuthor, setIsShowAuthor] = useState(false)
    const authorRef = useRef<HTMLDivElement>(null)
    const wrapRef = useRef<HTMLDivElement>(null)
    const commentRef = useRef<HTMLDivElement>(null)
    const [isComment, setIsComment] = useState(false)
    const onComment = () => {
        const commentDOM = commentRef.current!
        const wrapDOM = wrapRef.current!
        if (isComment) {
            wrapDOM.scrollTo(0, 0)
        } else {
            wrapDOM.scrollTo(0, commentDOM.offsetTop - 44)
        }
        setIsComment(!isComment)
    }
    const { info } = useInitState(() => getArticleInfo(articleId), 'article')
    const {
        results = [],
        total_count = -1,
        last_id,
        // end_id = '',
    } = useSelector((state: RootState) => state.article.comment)
    // const hasMore = last_id !== end_id
    const hasMore = results.length !== total_count
    const loadMore = async () => {
        console.log('加载更多~~')
        await dispatch(getCommentList(params.id, last_id))
    }
    const onFollowUser = async () => {
        await dispatch(followUser(info.aut_id, info.is_followed))
        Toast.show('操作成功')
    }
    const [commentShow, setCommentShow] = useState(false)
    const hideComment = () => {
        setCommentShow(false)
    }
    const showComment = () => {
        setCommentShow(true)
    }
    const onAddComment = async (comment: string) => {
        await dispatch(addComment(articleId, comment))
        await dispatch(getArticleInfo(articleId))
        Toast.show({ icon: 'success', content: '发表成功' })
    }
    // #1
    const [replyShow, setReplyShow] = useState({
        visible: false,
        origin: {} as Comment,
    })
    const hideReply = () => {
        setReplyShow({
            visible: false,
            origin: {} as Comment,
        })
    }
    const onReply = (comment: Comment) => {
        setReplyShow({
            visible: true,
            origin: comment,
        })
    }
    useEffect(() => {
        // 配置 highlight.js
        hljs.configure({
            // 忽略未经转义的 HTML 字符
            ignoreUnescapedHTML: true,
        })
        // 获取到内容中所有的code标签
        const codes = document.querySelectorAll('.dg-html pre code')
        codes.forEach((el) => {
            // 让code进行高亮
            hljs.highlightElement(el as HTMLElement)
        })
    }, [articleId])
    useEffect(() => {
        const wrapDOM = wrapRef.current!
        const authDOM = authorRef.current!
        const onScroll = function () {
            const rect = authDOM.getBoundingClientRect()!
            if (rect.top <= 0) {
                setIsShowAuthor(true)
            } else {
                setIsShowAuthor(false)
            }
        }
        wrapDOM.addEventListener('scroll', onScroll)
        return () => {
            wrapDOM.removeEventListener('scroll', onScroll)
        }
    }, [])
    useEffect(() => {
        return () => {
            dispatch(clearCommentList())
        }
    }, [dispatch])
    return (
        <div className={styles.root}>
            <div className='root-wrapper'>
                <NavBar
                    onBack={() => history.go(-1)}
                    right={
                        <span>
                            <Icon type='icongengduo' />
                        </span>
                    }
                >
                    {isShowAuthor && (
                        <div className='nav-author'>
                            <img src={info.aut_photo} alt='' />
                            <span className='name'>{info.aut_name}</span>
                            <span className={classNames('follow', info.is_followed ? 'followed' : '')} onClick={onFollowUser}>
                                {info.is_followed ? '已关注' : '关注'}
                            </span>
                        </div>
                    )}
                </NavBar>
                {/* //!文章详情和评论 */}
                <div className='wrapper' ref={wrapRef}>
                    <div className='article-wrapper'>
                        <div className='header'>
                            <h1 className='title'>{info.title}</h1>
                            <div className='info'>
                                <span>{dayjs(info.pubdate).format('YYYY-MM-DD')}</span>
                                <span>{info.read_count} 阅读</span>
                                <span>{info.comm_count} 评论</span>
                            </div>
                            <div className='author' ref={authorRef}>
                                <img src={info.aut_photo} alt='' />
                                <span className='name'>{info.aut_name}</span>
                                <span className={classNames('follow', info.is_followed ? 'followed' : '')} onClick={onFollowUser}>
                                    {info.is_followed ? '已关注' : '关注'}
                                </span>
                            </div>
                        </div>

                        <div className='content'>
                            <div
                                className='content-html dg-html'
                                dangerouslySetInnerHTML={{
                                    __html: dompurify.sanitize(info.content || ''),
                                }}
                            />
                            <div className='date'>发布文章时间：{dayjs(info.pubdate).format('YYYY-MM-DD')}</div>
                        </div>
                    </div>
                    {/* //!评论信息 */}
                    <div className='comment'>
                        <div className='comment-header' ref={commentRef}>
                            <span>全部评论（{info.comm_count}）</span>
                            <span>{info.like_count} 点赞</span>
                        </div>
                        <div className='comment-list'>
                            {info.comm_count === 0 ? <NoComment /> : results?.map((item) => <CommentItem key={item.com_id} comment={item} type='normal' onReply={onReply} />)}
                            <InfiniteScroll hasMore={hasMore} loadMore={loadMore} />
                        </div>
                    </div>
                </div>
                {/* //!底部评论栏 */}
                <CommentFooter onComment={onComment} showComment={showComment} />
            </div>
            <Popup visible={commentShow} position='right' destroyOnClose>
                <CommentInput hideComment={hideComment} onAddComment={onAddComment} />
            </Popup>
            <Popup visible={replyShow.visible} position='right' destroyOnClose>
                <CommentReply onHideReply={hideReply} />
            </Popup>
        </div>
    )
}

export default Article
```

`src\pages\Article\components\CommentItem\index.tsx`

```ts
import dayjs from 'dayjs'
import classnames from 'classnames'
import Icon from '@/components/Icon'
import styles from './index.module.scss'
import { Comment } from '@/types/data'

type Props = {
    // normal 普通 - 文章的评论
    // origin 回复评论的原始评论，也就是对哪个评论进行回复
    // reply 回复评论
    type?: 'normal' | 'reply' | 'origin'
    comment: Comment
    onReply: (comment: Comment) => void
}

const CommentItem = ({
    // normal 普通
    // origin 回复评论的原始评论
    // reply 回复评论
    type = 'normal',
    comment,
    onReply,
}: Props) => {
    // 回复按钮
    const replyJSX =
        type === 'normal' ? (
            <span className='replay' onClick={() => onReply && onReply(comment)}>
                {comment.reply_count} 回复
                <Icon type='iconbtn_right' />
            </span>
        ) : null

    return (
        <div className={styles.root}>
            <div className='avatar'>
                <img src={comment.aut_photo} alt='' />
            </div>
            <div className='comment-info'>
                <div className='comment-info-header'>
                    <span className='name'>{comment.aut_name}</span>
                    {/* 文章评论、评论的回复 */}
                    {(type === 'normal' || type === 'reply') && (
                        <span className='thumbs-up'>
                            {comment.like_count}
                            <Icon type={comment.is_liking ? 'iconbtn_like_sel' : 'iconbtn_like2'} />
                        </span>
                    )}
                    {/* 要回复的评论 */}
                    {type === 'origin' && <span className={classnames('follow', comment.is_followed ? 'followed' : '')}>{comment.is_followed ? '已关注' : '关注'}</span>}
                </div>
                <div className='comment-content'>{comment.content}</div>
                <div className='comment-footer'>
                    {replyJSX}
                    {/* 非评论的回复 */}
                    {type !== 'reply' && <span className='comment-time'>{dayjs(comment.pubdate).fromNow()}</span>}
                    {/* 文章的评论 */}
                    {type === 'origin' && (
                        <span className='thumbs-up'>
                            {comment.like_count}
                            <Icon type={comment.is_liking ? 'iconbtn_like_sel' : 'iconbtn_like2'} />
                        </span>
                    )}
                </div>
            </div>
        </div>
    )
}

export default CommentItem
```

`src\pages\Article\components\CommentReply\index.tsx`

```ts
import { NavBar } from 'antd-mobile'
import CommentFooter from '../CommentFooter'
import NoComment from '../NoComment'
import styles from './index.module.scss'
type Props = {
    onHideReply: () => void
}
export default function CommentReply({ onHideReply }: Props) {
    return (
        <div className={styles.root}>
            <div className='reply-wrapper'>
                {/* 顶部导航栏 */}
                <NavBar className='transparent-navbar' onBack={onHideReply}>
                    <div>{0}条回复</div>
                </NavBar>

                {/* 原评论信息 */}
                <div className='origin-comment'>原评论</div>

                {/* 回复评论的列表 */}
                <div className='reply-list'>
                    <div className='reply-header'>全部回复</div>

                    <NoComment />
                </div>

                {/* 评论工具栏，设置 type="reply" 不显示评论和点赞按钮 */}
                <CommentFooter type='reply' />
            </div>
        </div>
    )
}
```

## 评论回复-渲染原始评论

`src\pages\Article\index.tsx`

```ts
<CommentReply origin={replyShow.origin} onHideReply={hideReply} />
```

`src\pages\Article\components\CommentReply\index.tsx`

```ts
import { Comment } from '@/types/data'
import { NavBar } from 'antd-mobile'
import CommentFooter from '../CommentFooter'
import CommentItem from '../CommentItem'
import NoComment from '../NoComment'
import styles from './index.module.scss'
type Props = {
    onHideReply: () => void
    origin: Comment
}
export default function CommentReply({ onHideReply, origin }: Props) {
    return (
        <div className={styles.root}>
            <div className='reply-wrapper'>
                {/* 顶部导航栏 */}
                <NavBar className='transparent-navbar' onBack={onHideReply}>
                    <div>{origin.reply_count}条回复</div>
                </NavBar>
                {/* 原评论信息 */}
                <div className='origin-comment'>
                    <CommentItem comment={origin} type='origin' />
                </div>
                {/* 回复评论的列表 */}
                <div className='reply-list'>
                    <div className='reply-header'>全部回复</div>
                    <NoComment />
                </div>
                {/* 评论工具栏，设置 type="reply" 不显示评论和点赞按钮 */}
                <CommentFooter type='reply' />
            </div>
        </div>
    )
}
```

## 评论回复-渲染列表

1. `src\store\actions\article.ts`

```ts
export function getReplyList(id: string, offset: string): RootThunkAction {
    return async (dispatch) => {
        const res = await request.get<ApiResponse<CommentRes>>('/comments', {
            params: {
                type: 'c',
                source: id,
                offset,
            },
        })
        console.log(res.data.data)
    }
}
```

2. 测试：组件中发送请求并渲染。

`src\pages\Article\components\CommentReply\index.tsx`

```ts
import { useEffect, useState } from 'react'
import { ApiResponse, Comment, CommentRes } from '@/types/data'
import { NavBar } from 'antd-mobile'
import CommentFooter from '../CommentFooter'
import CommentItem from '../CommentItem'
import NoComment from '../NoComment'
import styles from './index.module.scss'
import request from '@/utils/request'
type Props = {
    onHideReply: () => void
    origin: Comment
}
export default function CommentReply({ onHideReply, origin }: Props) {
    let [reply, setReply] = useState<CommentRes>({} as CommentRes)
    useEffect(() => {
        const getReply = async () => {
            const res = await request.get<ApiResponse<CommentRes>>('/comments', {
                params: {
                    type: 'c',
                    source: origin.com_id,
                    offset: reply.last_id,
                },
            })
            setReply(res.data.data)
        }
        getReply()
    }, [])
    reply.results = reply.results ? reply.results : []
    return (
        <div className={styles.root}>
            <div className='reply-wrapper'>
                {/* 顶部导航栏 */}
                <NavBar className='transparent-navbar' onBack={onHideReply}>
                    <div>{origin.reply_count}条回复</div>
                </NavBar>
                {/* 原评论信息 */}
                <div className='origin-comment'>
                    <CommentItem comment={origin} type='origin' />
                </div>
                {/* 回复评论的列表 */}
                <div className='reply-list'>
                    <div className='reply-header'>全部回复</div>
                    {reply.results.length === 0 ? <NoComment /> : reply.results.map((item) => <CommentItem key={item.com_id} comment={item} type='reply' />)}
                </div>
                {/* 评论工具栏，设置 type="reply" 不显示评论和点赞按钮 */}
                <CommentFooter type='reply' />
            </div>
        </div>
    )
}
```

3. 加载更多。

```ts
import { useState } from 'react'
import { ApiResponse, Comment, CommentRes } from '@/types/data'
import { NavBar, InfiniteScroll } from 'antd-mobile'
import CommentFooter from '../CommentFooter'
import CommentItem from '../CommentItem'
import NoComment from '../NoComment'
import styles from './index.module.scss'
import request from '@/utils/request'
type Props = {
    onHideReply: () => void
    origin: Comment
}
export default function CommentReply({ onHideReply, origin }: Props) {
    const [reply, setReply] = useState<CommentRes>({} as CommentRes)
    const { last_id, end_id = '' } = reply
    const hasMore = last_id !== end_id
    const loadMore = async () => {
        const res = await request.get<ApiResponse<CommentRes>>('/comments', {
            params: {
                type: 'c',
                source: origin.com_id,
                offset: reply.last_id,
            },
        })
        setReply({
            ...res.data.data,
            results: [...(reply.results || []), ...res.data.data.results],
        })
    }
    return (
        <div className={styles.root}>
            <div className='reply-wrapper'>
                {/* 顶部导航栏 */}
                <NavBar className='transparent-navbar' onBack={onHideReply}>
                    <div>{origin.reply_count}条回复</div>
                </NavBar>
                {/* 原评论信息 */}
                <div className='origin-comment'>
                    <CommentItem comment={origin} type='origin' />
                </div>
                {/* 回复评论的列表 */}
                <div className='reply-list'>
                    <div className='reply-header'>全部回复</div>
                    {reply.results?.length === 0 ? <NoComment /> : reply.results?.map((item) => <CommentItem key={item.com_id} comment={item} type='reply' />)}
                    <InfiniteScroll hasMore={hasMore} loadMore={loadMore} />
                </div>
                {/* 评论工具栏，设置 type="reply" 不显示评论和点赞按钮 */}
                <CommentFooter type='reply' />
            </div>
        </div>
    )
}
```

## 评论回复-准备弹框

`src\pages\Article\components\CommentReply\index.tsx`

```ts
import { useState } from 'react'
import { ApiResponse, Comment, CommentRes } from '@/types/data'
import { NavBar, InfiniteScroll, Popup } from 'antd-mobile'
import CommentFooter from '../CommentFooter'
import CommentItem from '../CommentItem'
import NoComment from '../NoComment'
import styles from './index.module.scss'
import request from '@/utils/request'
import CommentInput from '../CommentInput'
type Props = {
    onHideReply: () => void
    origin: Comment
}
export default function CommentReply({ onHideReply, origin }: Props) {
    const [reply, setReply] = useState<CommentRes>({} as CommentRes)
    const { last_id, end_id = '' } = reply
    const hasMore = last_id !== end_id
    const loadMore = async () => {
        const res = await request.get<ApiResponse<CommentRes>>('/comments', {
            params: {
                type: 'c',
                source: origin.com_id,
                offset: reply.last_id,
            },
        })
        setReply({
            ...res.data.data,
            results: [...(reply.results || []), ...res.data.data.results],
        })
    }
    // #1
    const [show, setShow] = useState(false)
    const showComment = () => setShow(true)
    const hideComment = () => setShow(false)
    return (
        <div className={styles.root}>
            <div className='reply-wrapper'>
                {/* 顶部导航栏 */}
                <NavBar className='transparent-navbar' onBack={onHideReply}>
                    <div>{origin.reply_count}条回复</div>
                </NavBar>
                {/* 原评论信息 */}
                <div className='origin-comment'>
                    <CommentItem comment={origin} type='origin' />
                </div>
                {/* 回复评论的列表 */}
                <div className='reply-list'>
                    <div className='reply-header'>全部回复</div>
                    {reply.results?.length === 0 ? <NoComment /> : reply.results?.map((item) => <CommentItem key={item.com_id} comment={item} type='reply' />)}
                    <InfiniteScroll hasMore={hasMore} loadMore={loadMore} />
                </div>
                {/* 评论工具栏，设置 type="reply" 不显示评论和点赞按钮 */}
                <CommentFooter showComment={showComment} type='reply' />
            </div>
            {/* #2 */}
            <Popup visible={show} position='right' destroyOnClose>
                <CommentInput hideComment={hideComment} />
            </Popup>
        </div>
    )
}
```

## 评论回复-完成功能

```ts
import { useState } from 'react'
import { useSelector } from 'react-redux'
import { ApiResponse, Comment, CommentRes } from '@/types/data'
import { NavBar, InfiniteScroll, Popup } from 'antd-mobile'
import CommentFooter from '../CommentFooter'
import CommentItem from '../CommentItem'
import NoComment from '../NoComment'
import styles from './index.module.scss'
import request from '@/utils/request'
import CommentInput from '../CommentInput'
import { RootState } from '@/types/store'
type Props = {
    onHideReply: () => void
    origin: Comment
}
export default function CommentReply({ onHideReply, origin }: Props) {
    const [reply, setReply] = useState<CommentRes>({} as CommentRes)
    const { last_id, end_id = '' } = reply
    const hasMore = last_id !== end_id
    const loadMore = async () => {
        const res = await request.get<ApiResponse<CommentRes>>('/comments', {
            params: {
                type: 'c',
                source: origin.com_id,
                offset: reply.last_id,
            },
        })
        setReply({
            ...res.data.data,
            results: [...(reply.results || []), ...res.data.data.results],
        })
    }
    const [show, setShow] = useState(false)
    const showComment = () => setShow(true)
    const hideComment = () => setShow(false)
    const { art_id } = useSelector((state: RootState) => state.article.info)
    // !对评论进行回复
    const onAddComment = async (value: string) => {
        const res = await request.post<ApiResponse<{ new_obj: Comment }>>('/comments', {
            target: origin.com_id,
            content: value,
            art_id,
        })
        const old = reply.results || []
        setReply({
            ...reply,
            results: [res.data.data.new_obj, ...old],
        })
        hideComment()
    }
    return (
        <div className={styles.root}>
            <div className='reply-wrapper'>
                {/* 顶部导航栏 */}
                <NavBar className='transparent-navbar' onBack={onHideReply}>
                    <div>{origin.reply_count}条回复</div>
                </NavBar>
                {/* 原评论信息 */}
                <div className='origin-comment'>
                    <CommentItem comment={origin} type='origin' />
                </div>
                {/* 回复评论的列表 */}
                <div className='reply-list'>
                    <div className='reply-header'>全部回复</div>
                    {reply.results?.length === 0 ? <NoComment /> : reply.results?.map((item) => <CommentItem key={item.com_id} comment={item} type='reply' />)}
                    <InfiniteScroll hasMore={hasMore} loadMore={loadMore} />
                </div>
                {/* 评论工具栏，设置 type="reply" 不显示评论和点赞按钮 */}
                <CommentFooter showComment={showComment} type='reply' />
            </div>
            <Popup visible={show} position='right' destroyOnClose>
                <CommentInput onAddComment={onAddComment} hideComment={hideComment} name={origin.aut_name} />
            </Popup>
        </div>
    )
}
```

## 评论回复-数量 + 1

`src\types\store.d.ts`

```ts
export type ArticleAction =
    | {
          type: 'article/setArticleInfo'
          payload: ArticleDetail
      }
    | {
          type: 'article/saveComment'
          payload: CommentRes
      }
    | {
          type: 'article/clearComment'
      }
    | {
          type: 'article/saveNewComment'
          payload: Comment
      }
    | {
          type: 'article/addReplyCount'
          payload: string
      }
```

`src\store\reducers\article.ts`

```ts
import { ArticleDetail, CommentRes } from '@/types/data'
import { ArticleAction } from '@/types/store'
type ArticleType = {
    info: ArticleDetail
    comment: CommentRes
}
const initState: ArticleType = {
    info: {},
    comment: {},
} as ArticleType

export default function article(state = initState, action: ArticleAction): ArticleType {
    switch (action.type) {
        case 'article/setArticleInfo':
            return {
                ...state,
                info: action.payload,
            }
        case 'article/saveComment':
            const old = state.comment.results || []
            return {
                ...state,
                comment: {
                    ...action.payload,
                    results: [...old, ...action.payload.results],
                },
            }
        case 'article/clearComment':
            return {
                ...state,
                comment: {} as CommentRes,
            }
        case 'article/saveNewComment':
            return {
                ...state,
                comment: {
                    ...state.comment,
                    results: [action.payload, ...state.comment.results],
                },
            }
        case 'article/addReplyCount':
            return {
                ...state,
                comment: {
                    ...state.comment,
                    results: state.comment.results.map((item) => {
                        if (item.com_id === action.payload) {
                            return {
                                ...item,
                                reply_count: item.reply_count + 1,
                            }
                        } else {
                            return item
                        }
                    }),
                },
            }
        default:
            return state
    }
}
```

`src\store\actions\article.ts`

```ts
export function addReplyCount(commentId: string): ArticleAction {
    return {
        type: 'article/addReplyCount',
        payload: commentId,
    }
}
```

`src\pages\Article\components\CommentReply\index.tsx`

```ts
const onAddComment = async (value: string) => {
    const res = await request.post<ApiResponse<{ new_obj: Comment }>>('/comments', {
        target: origin.com_id,
        content: value,
        art_id,
    })
    const old = reply.results || []
    setReply({
        ...reply,
        results: [res.data.data.new_obj, ...old],
    })
    // 让评论数量 +1
    dispatch(addReplyCount(origin.com_id))
    hideComment()
}
```

## Tab 栏保存滚动位置

`src\pages\Home\index.tsx`

```ts
// 组件隐藏的时候不会销毁结构
<Tabs.Tab forceRender title={item.name} key={item.id}></Tabs.Tab>
```

## keep alive 功能实现

-   效果：从路由 A 切换到路由 B，再返回 路由 A，如果实现了 keep alive 效果，此时，路由 A 对应页面会继续保持原来状态。
    -   此处，要保留的状态，包含：页面滚动位置； Tab 高亮 等

实现方式：

1. 用 redux 存储这些数据：也就是在离开路由 A 的时候，用 redux 保存页面当前的滚动位置；Tab 高亮

-   将来返回该页面，从 redux 中读取这些数据，以回复原来页面的状态

2. 通过路由来实现

-   默认路由没有实现该功能，因为只要切换路由页面组件就会卸载，页面内容也就没有了。再返回该页面，页面相当于重新渲染，因此，原来的页面状态都会重置。也就没有实现保留上一次页面状态的效果了。
-   封装一个自定义路由，来实现即可
-   实现原理：在切换路由时，让页面不卸载，而是通过 display none 隐藏掉。这样，因为页面没有卸载，所以原来所有的操作都会被保存下来。 将来再返回该页面，只需要 display block 展示即可。这样，就可以恢复原来的内容了

`App.tsx`

```ts
// 用法：
// activePath 表示：要让哪个路由存活，也就是在切换到其他路由时，不是销毁该组件而是隐藏
//  注意： activePath 和 path 应该相同
import './App.scss'
import { Router, Route, Switch, Redirect } from 'react-router-dom'
import Login from './pages/Login'
import Layout from './pages/Layout'
import ProfileEdit from './pages/Profile/Edit'
import PrivateRoute from './components/PrivateRoute'
import history from './utils/history'
import Chat from './pages/Profile/Chat'
import Article from './pages/Article'
import Search from './pages/Search'
import SearchResult from './pages/Search/Result'
import KeepAlive from './components/KeepAlive'

export default function App() {
    return (
        <Router history={history}>
            {/* #1 */}
            <KeepAlive activePath='/home' path='/home'>
                <Layout />
            </KeepAlive>
            <div className='app'>
                <Switch>
                    <Route exact path='/' render={() => <Redirect to='/home' />} />
                    <Route path='/login' component={Login} />
                    {/* #2 */}
                    {/* <Route path='/home' component={Layout} /> */}
                    <PrivateRoute path='/profile/edit'>
                        <ProfileEdit />
                    </PrivateRoute>
                    <PrivateRoute path='/chat'>
                        <Chat />
                    </PrivateRoute>
                    <Route path='/article/:id' component={Article} />
                    <Route exact path='/search' component={Search} />
                    <Route path='/search/result' component={SearchResult} />
                </Switch>
            </div>
        </Router>
    )
}
```

`src\pages\Layout\index.tsx`

```ts
import styles from './index.module.scss'
import { TabBar } from 'antd-mobile'
import Icon from '@/components/Icon'
// !#1
import { useHistory, useLocation, Switch, Route } from 'react-router-dom'
import Home from '@/pages/Home'
import Question from '@/pages/Question'
import Video from '@/pages/Video'
import Profile from '@/pages/Profile'
import PrivateRoute from '@/components/PrivateRoute'
import KeepAlive from '@/components/KeepAlive'

const tabs = [
    { path: '/home', icon: 'iconbtn_home', text: '首页' },
    { path: '/home/question', icon: 'iconbtn_qa', text: '问答' },
    { path: '/home/video', icon: 'iconbtn_video', text: '视频' },
    { path: '/home/profile', icon: 'iconbtn_mine', text: '我的' },
]

export default function Layout() {
    const history = useHistory()
    const location = useLocation()
    const changeRoute = (path: string) => {
        history.push(path)
    }
    return (
        <div className={styles.root}>
            {/* #3 */}
            <KeepAlive activePath='/home' path='/home' exact>
                <Home />
            </KeepAlive>
            <Switch>
                {/* #4 */}
                {/* <Route path='/home' exact component={Home} /> */}
                <Route path='/home/question' exact component={Question} />
                <Route path='/home/video' exact component={Video} />
                <PrivateRoute path='/home/profile' exact>
                    <Profile />
                </PrivateRoute>
            </Switch>
            <TabBar className='tab-bar' onChange={changeRoute} activeKey={location.pathname}>
                {tabs.map((item) => (
                    <TabBar.Item key={item.path} icon={(active: boolean) => <Icon type={active ? `${item.icon}_sel` : item.icon} />} title={item.text} />
                ))}
            </TabBar>
        </div>
    )
}
```
